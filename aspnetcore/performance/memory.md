---
title: Administración de memoria y patrones en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo se administra la memoria en ASP.NET Core y cómo funciona el recolector de elementos no utilizados (GC).
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440953"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="d9020-103">Administración de memoria y recolección de elementos no utilizados (GC) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9020-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="d9020-104">Por [Sébastien Ros](https://github.com/sebastienros) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9020-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d9020-105">La administración de memoria es compleja, incluso en un marco administrado como .NET.</span><span class="sxs-lookup"><span data-stu-id="d9020-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="d9020-106">Analizar y comprender los problemas de memoria puede ser un desafío.</span><span class="sxs-lookup"><span data-stu-id="d9020-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="d9020-107">Este artículo:</span><span class="sxs-lookup"><span data-stu-id="d9020-107">This article:</span></span>

* <span data-ttu-id="d9020-108">Fue motivado por muchas fugas de *memoria* y *GC no problemas de trabajo.*</span><span class="sxs-lookup"><span data-stu-id="d9020-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="d9020-109">La mayoría de estos problemas se debieron a no comprender cómo funciona el consumo de memoria en .NET Core o a no comprender cómo se mide.</span><span class="sxs-lookup"><span data-stu-id="d9020-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="d9020-110">Muestra el uso problemático de la memoria y sugiere enfoques alternativos.</span><span class="sxs-lookup"><span data-stu-id="d9020-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="d9020-111">Cómo funciona la recolección de elementos no utilizados (GC) en .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9020-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="d9020-112">El GC asigna segmentos de montón donde cada segmento es un rango contiguo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="d9020-113">Los objetos colocados en el montón se clasifican en una de 3 generaciones: 0, 1 o 2.</span><span class="sxs-lookup"><span data-stu-id="d9020-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="d9020-114">La generación determina la frecuencia con la que el GC intenta liberar memoria en objetos administrados a los que ya no hace referencia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d9020-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="d9020-115">Las generaciones numeradas más bajas son GC'd con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="d9020-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="d9020-116">Los objetos se mueven de una generación a otra en función de su duración.</span><span class="sxs-lookup"><span data-stu-id="d9020-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="d9020-117">A medida que los objetos viven más tiempo, se mueven a una generación superior.</span><span class="sxs-lookup"><span data-stu-id="d9020-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="d9020-118">Como se mencionó anteriormente, las generaciones superiores son GC'd menos a menudo.</span><span class="sxs-lookup"><span data-stu-id="d9020-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="d9020-119">Los objetos vividos a corto plazo siempre permanecen en la generación 0.</span><span class="sxs-lookup"><span data-stu-id="d9020-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="d9020-120">Por ejemplo, los objetos a los que se hace referencia durante la vida de una solicitud web son de corta duración.</span><span class="sxs-lookup"><span data-stu-id="d9020-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="d9020-121">Los [singletons](xref:fundamentals/dependency-injection#service-lifetimes) de nivel de aplicación generalmente migran a la generación 2.</span><span class="sxs-lookup"><span data-stu-id="d9020-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="d9020-122">Cuando se inicia una aplicación ASP.NET Core, el GC:</span><span class="sxs-lookup"><span data-stu-id="d9020-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="d9020-123">Reserva algo de memoria para los segmentos de montón iniciales.</span><span class="sxs-lookup"><span data-stu-id="d9020-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="d9020-124">Confirma una pequeña parte de la memoria cuando se carga el tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="d9020-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="d9020-125">Las asignaciones de memoria anteriores se realizan por motivos de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="d9020-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="d9020-126">La ventaja de rendimiento proviene de segmentos de montón en memoria contigua.</span><span class="sxs-lookup"><span data-stu-id="d9020-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="d9020-127">Llama a GC. Recoger</span><span class="sxs-lookup"><span data-stu-id="d9020-127">Call GC.Collect</span></span>

<span data-ttu-id="d9020-128">Llamando a [GC. Recopilar](xref:System.GC.Collect*) explícitamente:</span><span class="sxs-lookup"><span data-stu-id="d9020-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="d9020-129">**No** debe realizarse mediante la producción ASP.NET aplicaciones principales.</span><span class="sxs-lookup"><span data-stu-id="d9020-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="d9020-130">Es útil cuando se investigan pérdidas de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="d9020-131">Al investigar, comprueba que el GC ha quitado todos los objetos colgantes de la memoria para que se pueda medir la memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="d9020-132">Analizar el uso de memoria de una aplicación</span><span class="sxs-lookup"><span data-stu-id="d9020-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="d9020-133">Las herramientas dedicadas pueden ayudar a analizar el uso de memoria:</span><span class="sxs-lookup"><span data-stu-id="d9020-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="d9020-134">Recuento de referencias a objetos</span><span class="sxs-lookup"><span data-stu-id="d9020-134">Counting object references</span></span>
- <span data-ttu-id="d9020-135">Medición de cuánto impacto tiene el GC en el uso de la CPU</span><span class="sxs-lookup"><span data-stu-id="d9020-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="d9020-136">Medición del espacio de memoria utilizado para cada generación</span><span class="sxs-lookup"><span data-stu-id="d9020-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="d9020-137">Utilice las siguientes herramientas para analizar el uso de memoria:</span><span class="sxs-lookup"><span data-stu-id="d9020-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="d9020-138">[traza de puntos](/dotnet/core/diagnostics/dotnet-trace): Se puede utilizar en máquinas de producción.</span><span class="sxs-lookup"><span data-stu-id="d9020-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="d9020-139">Analizar el uso de memoria sin el depurador de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9020-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="d9020-140">Análisis del uso de memoria en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9020-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="d9020-141">Detección de problemas de memoria</span><span class="sxs-lookup"><span data-stu-id="d9020-141">Detecting memory issues</span></span>

<span data-ttu-id="d9020-142">El Administrador de tareas se puede usar para hacerse una idea de la cantidad de memoria que usa una aplicación ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d9020-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="d9020-143">El valor de memoria del Administrador de tareas:</span><span class="sxs-lookup"><span data-stu-id="d9020-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="d9020-144">Representa la cantidad de memoria que usa el proceso de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d9020-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="d9020-145">Incluye los objetos vivos de la aplicación y otros consumidores de memoria, como el uso de memoria nativa.</span><span class="sxs-lookup"><span data-stu-id="d9020-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="d9020-146">Si el valor de memoria del Administrador de tareas aumenta indefinidamente y nunca se aplana, la aplicación tiene una pérdida de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="d9020-147">En las secciones siguientes se muestran y se explican varios patrones de uso de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="d9020-148">Ejemplo de aplicación de uso de memoria de pantalla</span><span class="sxs-lookup"><span data-stu-id="d9020-148">Sample display memory usage app</span></span>

<span data-ttu-id="d9020-149">La aplicación de [ejemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponible en GitHub.</span><span class="sxs-lookup"><span data-stu-id="d9020-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="d9020-150">La aplicación MemoryLeak:</span><span class="sxs-lookup"><span data-stu-id="d9020-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="d9020-151">Incluye un controlador de diagnóstico que recopila memoria en tiempo real y datos DE GC para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d9020-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="d9020-152">Tiene una página de índice que muestra la memoria y los datos de GC.</span><span class="sxs-lookup"><span data-stu-id="d9020-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="d9020-153">La página Index se actualiza cada segundo.</span><span class="sxs-lookup"><span data-stu-id="d9020-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="d9020-154">Contiene un controlador de API que proporciona varios patrones de carga de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="d9020-155">No es una herramienta compatible, sin embargo, se puede utilizar para mostrar patrones de uso de memoria de ASP.NET aplicaciones principales.</span><span class="sxs-lookup"><span data-stu-id="d9020-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="d9020-156">Ejecute MemoryLeak.</span><span class="sxs-lookup"><span data-stu-id="d9020-156">Run MemoryLeak.</span></span> <span data-ttu-id="d9020-157">La memoria asignada aumenta lentamente hasta que se produce un GC.</span><span class="sxs-lookup"><span data-stu-id="d9020-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="d9020-158">La memoria aumenta porque la herramienta asigna un objeto personalizado para capturar datos.</span><span class="sxs-lookup"><span data-stu-id="d9020-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="d9020-159">La siguiente imagen muestra la página MemoryLeak Index cuando se produce un GC Gen 0.</span><span class="sxs-lookup"><span data-stu-id="d9020-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="d9020-160">El gráfico muestra 0 RPS (solicitudes por segundo) porque no se ha llamado a ningún punto de conexión de API del controlador de API.</span><span class="sxs-lookup"><span data-stu-id="d9020-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![gráfico anterior](memory/_static/0RPS.png)

<span data-ttu-id="d9020-162">El gráfico muestra dos valores para el uso de memoria:</span><span class="sxs-lookup"><span data-stu-id="d9020-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="d9020-163">Asignado: la cantidad de memoria ocupada por objetos gestionados</span><span class="sxs-lookup"><span data-stu-id="d9020-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="d9020-164">Conjunto de [trabajo:](/windows/win32/memory/working-set)el conjunto de páginas en el espacio de direcciones virtuales del proceso que residen actualmente en la memoria física.</span><span class="sxs-lookup"><span data-stu-id="d9020-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="d9020-165">El conjunto de trabajo que se muestra es el mismo valor que muestra el Administrador de tareas.</span><span class="sxs-lookup"><span data-stu-id="d9020-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="d9020-166">Objetos transitorios</span><span class="sxs-lookup"><span data-stu-id="d9020-166">Transient objects</span></span>

<span data-ttu-id="d9020-167">La siguiente API crea una instancia string de 10 KB y la devuelve al cliente.</span><span class="sxs-lookup"><span data-stu-id="d9020-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="d9020-168">En cada solicitud, se asigna un nuevo objeto en la memoria y se escribe en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="d9020-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="d9020-169">Las cadenas se almacenan como caracteres UTF-16 en .NET, por lo que cada carácter toma 2 bytes en memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="d9020-170">El siguiente gráfico se genera con una carga relativamente pequeña para mostrar cómo las asignaciones de memoria se ven afectadas por el GC.</span><span class="sxs-lookup"><span data-stu-id="d9020-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![gráfico anterior](memory/_static/bigstring.png)

<span data-ttu-id="d9020-172">El gráfico anterior muestra:</span><span class="sxs-lookup"><span data-stu-id="d9020-172">The preceding chart shows:</span></span>

* <span data-ttu-id="d9020-173">4K RPS (Solicitudes por segundo).</span><span class="sxs-lookup"><span data-stu-id="d9020-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="d9020-174">Las colecciones de GC de generación 0 se producen aproximadamente cada dos segundos.</span><span class="sxs-lookup"><span data-stu-id="d9020-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="d9020-175">El conjunto de trabajo es constante en aproximadamente 500 MB.</span><span class="sxs-lookup"><span data-stu-id="d9020-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="d9020-176">CPU es 12%.</span><span class="sxs-lookup"><span data-stu-id="d9020-176">CPU is 12%.</span></span>
* <span data-ttu-id="d9020-177">El consumo y la liberación de memoria (a través de GC) es estable.</span><span class="sxs-lookup"><span data-stu-id="d9020-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="d9020-178">El siguiente gráfico se toma al rendimiento máximo que puede controlar la máquina.</span><span class="sxs-lookup"><span data-stu-id="d9020-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![gráfico anterior](memory/_static/bigstring2.png)

<span data-ttu-id="d9020-180">El gráfico anterior muestra:</span><span class="sxs-lookup"><span data-stu-id="d9020-180">The preceding chart shows:</span></span>

* <span data-ttu-id="d9020-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="d9020-181">22K RPS</span></span>
* <span data-ttu-id="d9020-182">Las colecciones de GC de generación 0 se producen varias veces por segundo.</span><span class="sxs-lookup"><span data-stu-id="d9020-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="d9020-183">Las colecciones de generación 1 se desencadenan porque la aplicación asignó significativamente más memoria por segundo.</span><span class="sxs-lookup"><span data-stu-id="d9020-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="d9020-184">El conjunto de trabajo es constante en aproximadamente 500 MB.</span><span class="sxs-lookup"><span data-stu-id="d9020-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="d9020-185">CPU es 33%.</span><span class="sxs-lookup"><span data-stu-id="d9020-185">CPU is 33%.</span></span>
* <span data-ttu-id="d9020-186">El consumo y la liberación de memoria (a través de GC) es estable.</span><span class="sxs-lookup"><span data-stu-id="d9020-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="d9020-187">La CPU (33%) no se utiliza en exceso, por lo tanto, la recolección de elementos no utilizados puede mantenerse al día con un gran número de asignaciones.</span><span class="sxs-lookup"><span data-stu-id="d9020-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="d9020-188">Workstation GC vs. Server GC</span><span class="sxs-lookup"><span data-stu-id="d9020-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="d9020-189">El recolector de elementos no utilizados de .NET tiene dos modos diferentes:</span><span class="sxs-lookup"><span data-stu-id="d9020-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="d9020-190">**Workstation GC**: Optimizado para el escritorio.</span><span class="sxs-lookup"><span data-stu-id="d9020-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="d9020-191">**Server GC**.</span><span class="sxs-lookup"><span data-stu-id="d9020-191">**Server GC**.</span></span> <span data-ttu-id="d9020-192">El GC predeterminado para las aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d9020-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="d9020-193">Optimizado para el servidor.</span><span class="sxs-lookup"><span data-stu-id="d9020-193">Optimized for the server.</span></span>

<span data-ttu-id="d9020-194">El modo GC se puede establecer explícitamente en el archivo de proyecto o en el archivo *runtimeconfig.json* de la aplicación publicada.</span><span class="sxs-lookup"><span data-stu-id="d9020-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="d9020-195">El marcado siguiente `ServerGarbageCollection` muestra la configuración en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="d9020-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="d9020-196">Cambiar `ServerGarbageCollection` en el archivo de proyecto requiere que la aplicación se vuelva a generar.</span><span class="sxs-lookup"><span data-stu-id="d9020-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="d9020-197">**Nota:** La recolección de elementos no utilizados del servidor **no** está disponible en equipos con un solo núcleo.</span><span class="sxs-lookup"><span data-stu-id="d9020-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="d9020-198">Para obtener más información, vea <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="d9020-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="d9020-199">La siguiente imagen muestra el perfil de memoria bajo un RPS 5K mediante la GC de la estación de trabajo.</span><span class="sxs-lookup"><span data-stu-id="d9020-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![gráfico anterior](memory/_static/workstation.png)

<span data-ttu-id="d9020-201">Las diferencias entre este gráfico y la versión del servidor son significativas:</span><span class="sxs-lookup"><span data-stu-id="d9020-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="d9020-202">El conjunto de trabajo cae de 500 MB a 70 MB.</span><span class="sxs-lookup"><span data-stu-id="d9020-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="d9020-203">El GC genera 0 colecciones varias veces por segundo en lugar de cada dos segundos.</span><span class="sxs-lookup"><span data-stu-id="d9020-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="d9020-204">GC cae de 300 MB a 10 MB.</span><span class="sxs-lookup"><span data-stu-id="d9020-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="d9020-205">En un entorno de servidor web típico, el uso de CPU es más importante que la memoria, por lo tanto, el GC del servidor es mejor.</span><span class="sxs-lookup"><span data-stu-id="d9020-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="d9020-206">Si la utilización de memoria es alta y el uso de la CPU es relativamente bajo, el GC de la estación de trabajo pudo ser más eficaz.</span><span class="sxs-lookup"><span data-stu-id="d9020-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="d9020-207">Por ejemplo, alta densidad alojando varias aplicaciones web donde la memoria es escasa.</span><span class="sxs-lookup"><span data-stu-id="d9020-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="d9020-208">GC con Docker y contenedores pequeños</span><span class="sxs-lookup"><span data-stu-id="d9020-208">GC using Docker and small containers</span></span>

<span data-ttu-id="d9020-209">Cuando se ejecutan varias aplicaciones en contenedores en un equipo, Workstation GC puede ser más anterior que Server GC.</span><span class="sxs-lookup"><span data-stu-id="d9020-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="d9020-210">Para obtener más información, consulte [Ejecución con GC de servidor en un contenedor pequeño](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) y Ejecución con GC de servidor en un escenario de contenedor pequeño Parte 1 – Límite duro para el [montón](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)de GC .</span><span class="sxs-lookup"><span data-stu-id="d9020-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="d9020-211">Referencias a objetos persistentes</span><span class="sxs-lookup"><span data-stu-id="d9020-211">Persistent object references</span></span>

<span data-ttu-id="d9020-212">El GC no puede liberar objetos a los que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="d9020-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="d9020-213">Los objetos a los que se hace referencia pero que ya no son necesarios provocan una pérdida de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="d9020-214">Si la aplicación asigna objetos con frecuencia y no los libera después de que ya no se necesitan, el uso de memoria aumentará con el tiempo.</span><span class="sxs-lookup"><span data-stu-id="d9020-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="d9020-215">La siguiente API crea una instancia string de 10 KB y la devuelve al cliente.</span><span class="sxs-lookup"><span data-stu-id="d9020-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="d9020-216">La diferencia con el ejemplo anterior es que un miembro estático hace referencia a esta instancia, lo que significa que nunca está disponible para su recopilación.</span><span class="sxs-lookup"><span data-stu-id="d9020-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="d9020-217">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="d9020-217">The preceding code:</span></span>

* <span data-ttu-id="d9020-218">Es un ejemplo de una pérdida de memoria típica.</span><span class="sxs-lookup"><span data-stu-id="d9020-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="d9020-219">Con las llamadas frecuentes, hace que la memoria `OutOfMemory` de la aplicación aumente hasta que el proceso se bloquee con una excepción.</span><span class="sxs-lookup"><span data-stu-id="d9020-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![gráfico anterior](memory/_static/eternal.png)

<span data-ttu-id="d9020-221">En la imagen anterior:</span><span class="sxs-lookup"><span data-stu-id="d9020-221">In the preceding image:</span></span>

* <span data-ttu-id="d9020-222">Las pruebas `/api/staticstring` de carga del punto de conexión provocan un aumento lineal de la memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="d9020-223">El GC intenta liberar memoria a medida que crece la presión de memoria, llamando a una colección de generación 2.</span><span class="sxs-lookup"><span data-stu-id="d9020-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="d9020-224">El GC no puede liberar la memoria filtrada.</span><span class="sxs-lookup"><span data-stu-id="d9020-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="d9020-225">El conjunto asignado y de trabajo aumenta con el tiempo.</span><span class="sxs-lookup"><span data-stu-id="d9020-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="d9020-226">Algunos escenarios, como el almacenamiento en caché, requieren que se mantengan las referencias a objetos hasta que la presión de memoria obligue a liberarlas.</span><span class="sxs-lookup"><span data-stu-id="d9020-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="d9020-227">La <xref:System.WeakReference> clase se puede utilizar para este tipo de código de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="d9020-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="d9020-228">Un `WeakReference` objeto se recopila bajo presiones de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="d9020-229">La implementación <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`predeterminada de uses .</span><span class="sxs-lookup"><span data-stu-id="d9020-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="d9020-230">Memoria nativa</span><span class="sxs-lookup"><span data-stu-id="d9020-230">Native memory</span></span>

<span data-ttu-id="d9020-231">Algunos objetos de .NET Core dependen de la memoria nativa.</span><span class="sxs-lookup"><span data-stu-id="d9020-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="d9020-232">La memoria nativa **no** puede ser recopilada por el GC.</span><span class="sxs-lookup"><span data-stu-id="d9020-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="d9020-233">El objeto .NET con memoria nativa debe liberarlo mediante código nativo.</span><span class="sxs-lookup"><span data-stu-id="d9020-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="d9020-234">.NET proporciona <xref:System.IDisposable> la interfaz para permitir que los desarrolladores liberen memoria nativa.</span><span class="sxs-lookup"><span data-stu-id="d9020-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="d9020-235">Incluso <xref:System.IDisposable.Dispose*> si no se llama, `Dispose` las clases implementadas correctamente llaman cuando se ejecuta el [finalizador.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)</span><span class="sxs-lookup"><span data-stu-id="d9020-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="d9020-236">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d9020-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="d9020-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) es una clase administrada, por lo que cualquier instancia se recopilará al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d9020-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="d9020-238">La siguiente imagen muestra el perfil `fileprovider` de memoria al invocar la API continuamente.</span><span class="sxs-lookup"><span data-stu-id="d9020-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![gráfico anterior](memory/_static/fileprovider.png)

<span data-ttu-id="d9020-240">El gráfico anterior muestra un problema obvio con la implementación de esta clase, ya que sigue aumentando el uso de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="d9020-241">Este es un problema conocido que se está rastreando en [este problema.](https://github.com/dotnet/aspnetcore/issues/3110)</span><span class="sxs-lookup"><span data-stu-id="d9020-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="d9020-242">La misma fuga podría ocurrir en el código de usuario, por uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="d9020-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="d9020-243">No liberar la clase correctamente.</span><span class="sxs-lookup"><span data-stu-id="d9020-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="d9020-244">Olvidarse de `Dispose`invocar el método de los objetos dependientes que se deben eliminar.</span><span class="sxs-lookup"><span data-stu-id="d9020-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="d9020-245">Montón de objetos grandes</span><span class="sxs-lookup"><span data-stu-id="d9020-245">Large objects heap</span></span>

<span data-ttu-id="d9020-246">La asignación de memoria frecuente o los ciclos libres pueden fragmentar la memoria, especialmente cuando se asignan grandes fragmentos de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="d9020-247">Los objetos se asignan en bloques contiguos de memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="d9020-248">Para mitigar la fragmentación, cuando el GC libera memoria, intenta desfragmentarla.</span><span class="sxs-lookup"><span data-stu-id="d9020-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="d9020-249">Este proceso se denomina **compactación**.</span><span class="sxs-lookup"><span data-stu-id="d9020-249">This process is called **compaction**.</span></span> <span data-ttu-id="d9020-250">La compactación implica mover objetos.</span><span class="sxs-lookup"><span data-stu-id="d9020-250">Compaction involves moving objects.</span></span> <span data-ttu-id="d9020-251">Mover objetos grandes impone una penalización de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="d9020-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="d9020-252">Por esta razón, el GC crea una zona de memoria especial para objetos _grandes,_ denominada montón de [objetos grandes](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span><span class="sxs-lookup"><span data-stu-id="d9020-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="d9020-253">Los objetos que son mayores que 85.000 bytes (aproximadamente 83 KB) son:</span><span class="sxs-lookup"><span data-stu-id="d9020-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="d9020-254">Colocado en la LOH.</span><span class="sxs-lookup"><span data-stu-id="d9020-254">Placed on the LOH.</span></span>
* <span data-ttu-id="d9020-255">No compactado.</span><span class="sxs-lookup"><span data-stu-id="d9020-255">Not compacted.</span></span>
* <span data-ttu-id="d9020-256">Recolectado durante la generación de 2 GCs.</span><span class="sxs-lookup"><span data-stu-id="d9020-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="d9020-257">Cuando el LOH está lleno, el GC desencadenará una colección de generación 2.</span><span class="sxs-lookup"><span data-stu-id="d9020-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="d9020-258">Colecciones de generación 2:</span><span class="sxs-lookup"><span data-stu-id="d9020-258">Generation 2 collections:</span></span>

* <span data-ttu-id="d9020-259">Son intrínsecamente lentos.</span><span class="sxs-lookup"><span data-stu-id="d9020-259">Are inherently slow.</span></span>
* <span data-ttu-id="d9020-260">Además, incurrir en el costo de desencadenar una colección en todas las demás generaciones.</span><span class="sxs-lookup"><span data-stu-id="d9020-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="d9020-261">El código siguiente compacta el LOH inmediatamente:</span><span class="sxs-lookup"><span data-stu-id="d9020-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="d9020-262">Consulte <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> para obtener información sobre la compactación del LOH.</span><span class="sxs-lookup"><span data-stu-id="d9020-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="d9020-263">En los contenedores que usan .NET Core 3.0 y versiones posteriores, el LOH se compacta automáticamente.</span><span class="sxs-lookup"><span data-stu-id="d9020-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="d9020-264">La siguiente API que ilustra este comportamiento:</span><span class="sxs-lookup"><span data-stu-id="d9020-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="d9020-265">El siguiente gráfico muestra el `/api/loh/84975` perfil de memoria de llamar al punto de conexión, bajo carga máxima:</span><span class="sxs-lookup"><span data-stu-id="d9020-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![gráfico anterior](memory/_static/loh1.png)

<span data-ttu-id="d9020-267">El siguiente gráfico muestra el `/api/loh/84976` perfil de memoria de llamar al punto de conexión, asignando *solo un byte más:*</span><span class="sxs-lookup"><span data-stu-id="d9020-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![gráfico anterior](memory/_static/loh2.png)

<span data-ttu-id="d9020-269">Nota: `byte[]` La estructura tiene bytes de sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="d9020-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="d9020-270">Es por eso que 84.976 bytes desencadena el límite de 85.000.</span><span class="sxs-lookup"><span data-stu-id="d9020-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="d9020-271">Comparación de los dos gráficos anteriores:</span><span class="sxs-lookup"><span data-stu-id="d9020-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="d9020-272">El conjunto de trabajo es similar para ambos escenarios, alrededor de 450 MB.</span><span class="sxs-lookup"><span data-stu-id="d9020-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="d9020-273">Las solicitudes SUB LOH (84.975 bytes) muestran principalmente las colecciones de generación 0.</span><span class="sxs-lookup"><span data-stu-id="d9020-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="d9020-274">Las solicitudes de sobre LOH generan colecciones de generación constante 2.</span><span class="sxs-lookup"><span data-stu-id="d9020-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="d9020-275">Las colecciones de generación 2 son caras.</span><span class="sxs-lookup"><span data-stu-id="d9020-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="d9020-276">Se requiere más CPU y el rendimiento disminuye casi un 50%.</span><span class="sxs-lookup"><span data-stu-id="d9020-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="d9020-277">Los objetos grandes temporales son particularmente problemáticos porque causan gén2 GCs.</span><span class="sxs-lookup"><span data-stu-id="d9020-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="d9020-278">Para obtener el máximo rendimiento, se debe minimizar el uso de objetos grandes.</span><span class="sxs-lookup"><span data-stu-id="d9020-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="d9020-279">Si es posible, divida objetos grandes.</span><span class="sxs-lookup"><span data-stu-id="d9020-279">If possible, split up large objects.</span></span> <span data-ttu-id="d9020-280">Por ejemplo, el middleware [de almacenamiento en caché](xref:performance/caching/response) de respuestas en ASP.NET Core divide las entradas de caché en bloques de menos de 85.000 bytes.</span><span class="sxs-lookup"><span data-stu-id="d9020-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="d9020-281">Los vínculos siguientes muestran el enfoque ASP.NET Core para mantener los objetos por debajo del límite loH:</span><span class="sxs-lookup"><span data-stu-id="d9020-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="d9020-282">ResponseCaching/Streams/StreamUtilities.cs</span><span class="sxs-lookup"><span data-stu-id="d9020-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="d9020-283">ResponseCaching/MemoryResponseCache.cs</span><span class="sxs-lookup"><span data-stu-id="d9020-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="d9020-284">Para más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="d9020-284">For more information, see:</span></span>

* [<span data-ttu-id="d9020-285">Montón de objetos grandes descubiertos</span><span class="sxs-lookup"><span data-stu-id="d9020-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="d9020-286">Montón de objetos grandes</span><span class="sxs-lookup"><span data-stu-id="d9020-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="d9020-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="d9020-287">HttpClient</span></span>

<span data-ttu-id="d9020-288">El <xref:System.Net.Http.HttpClient> uso incorrecto puede provocar una fuga de recursos.</span><span class="sxs-lookup"><span data-stu-id="d9020-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="d9020-289">Recursos del sistema, como conexiones de base de datos, sockets, identificadores de archivos, etc.:</span><span class="sxs-lookup"><span data-stu-id="d9020-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="d9020-290">Son más escasos que la memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="d9020-291">Son más problemáticos cuando se pierden que la memoria.</span><span class="sxs-lookup"><span data-stu-id="d9020-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="d9020-292">Los desarrolladores experimentados <xref:System.IDisposable.Dispose*> de .NET <xref:System.IDisposable>saben llamar a objetos que implementan .</span><span class="sxs-lookup"><span data-stu-id="d9020-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="d9020-293">No eliminar objetos `IDisposable` que implementan normalmente da como resultado memoria con pérdida o recursos del sistema filtrados.</span><span class="sxs-lookup"><span data-stu-id="d9020-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="d9020-294">`HttpClient`implementos `IDisposable`, pero **no** deben eliminarse en cada invocación.</span><span class="sxs-lookup"><span data-stu-id="d9020-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="d9020-295">Más `HttpClient` bien, debe ser reutilizado.</span><span class="sxs-lookup"><span data-stu-id="d9020-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="d9020-296">El siguiente punto de conexión `HttpClient` crea y elimina una nueva instancia en cada solicitud:</span><span class="sxs-lookup"><span data-stu-id="d9020-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="d9020-297">En la carga, se registran los siguientes mensajes de error:</span><span class="sxs-lookup"><span data-stu-id="d9020-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="d9020-298">Aunque las `HttpClient` instancias se eliminan, la conexión de red real tarda algún tiempo en ser liberada por el sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="d9020-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="d9020-299">Al crear continuamente nuevas conexiones, se produce el agotamiento de _los puertos._</span><span class="sxs-lookup"><span data-stu-id="d9020-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="d9020-300">Cada conexión de cliente requiere su propio puerto de cliente.</span><span class="sxs-lookup"><span data-stu-id="d9020-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="d9020-301">Una manera de prevenir el agotamiento `HttpClient` del puerto es reutilizar la misma instancia:</span><span class="sxs-lookup"><span data-stu-id="d9020-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="d9020-302">La `HttpClient` instancia se libera cuando se detiene la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d9020-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="d9020-303">Este ejemplo muestra que no todos los recursos desechables deben eliminarse después de cada uso.</span><span class="sxs-lookup"><span data-stu-id="d9020-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="d9020-304">Consulte lo siguiente para obtener una mejor `HttpClient` manera de controlar la duración de una instancia:</span><span class="sxs-lookup"><span data-stu-id="d9020-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="d9020-305">HttpClient y administración de la duración</span><span class="sxs-lookup"><span data-stu-id="d9020-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="d9020-306">Blog de fábrica HTTPClient</span><span class="sxs-lookup"><span data-stu-id="d9020-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="d9020-307">Agrupación de objetos</span><span class="sxs-lookup"><span data-stu-id="d9020-307">Object pooling</span></span>

<span data-ttu-id="d9020-308">En el ejemplo `HttpClient` anterior se muestra cómo se puede hacer estática y reutilizar la instancia por todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d9020-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="d9020-309">La reutilización impide quedarse sin recursos.</span><span class="sxs-lookup"><span data-stu-id="d9020-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="d9020-310">Agrupación de objetos:</span><span class="sxs-lookup"><span data-stu-id="d9020-310">Object pooling:</span></span>

* <span data-ttu-id="d9020-311">Utiliza el patrón de reutilización.</span><span class="sxs-lookup"><span data-stu-id="d9020-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="d9020-312">Está diseñado para objetos que son caros de crear.</span><span class="sxs-lookup"><span data-stu-id="d9020-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="d9020-313">Un grupo es una colección de objetos inicializados previamente que se pueden reservar y liberar entre subprocesos.</span><span class="sxs-lookup"><span data-stu-id="d9020-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="d9020-314">Los grupos pueden definir reglas de asignación como límites, tamaños predefinidos o tasa de crecimiento.</span><span class="sxs-lookup"><span data-stu-id="d9020-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="d9020-315">El paquete NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contiene clases que ayudan a administrar dichos grupos.</span><span class="sxs-lookup"><span data-stu-id="d9020-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="d9020-316">El siguiente punto de `byte` conexión de API crea una instancia de un búfer que se rellena con números aleatorios en cada solicitud:</span><span class="sxs-lookup"><span data-stu-id="d9020-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="d9020-317">En la siguiente pantalla de gráfico se muestra la llamada a la API anterior con carga moderada:</span><span class="sxs-lookup"><span data-stu-id="d9020-317">The following chart display calling the preceding API with moderate load:</span></span>

![gráfico anterior](memory/_static/array.png)

<span data-ttu-id="d9020-319">En el gráfico anterior, las colecciones de generación 0 se producen aproximadamente una vez por segundo.</span><span class="sxs-lookup"><span data-stu-id="d9020-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="d9020-320">El código anterior se puede optimizar `byte` agrupando el búfer mediante [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span><span class="sxs-lookup"><span data-stu-id="d9020-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="d9020-321">Una instancia estática se reutiliza entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d9020-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="d9020-322">Lo que es diferente con este enfoque es que un objeto agrupado se devuelve desde la API.</span><span class="sxs-lookup"><span data-stu-id="d9020-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="d9020-323">Eso significa:</span><span class="sxs-lookup"><span data-stu-id="d9020-323">That means:</span></span>

* <span data-ttu-id="d9020-324">El objeto está fuera del control tan pronto como se vuelve del método.</span><span class="sxs-lookup"><span data-stu-id="d9020-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="d9020-325">No se puede liberar el objeto.</span><span class="sxs-lookup"><span data-stu-id="d9020-325">You can't release the object.</span></span>

<span data-ttu-id="d9020-326">Para configurar la eliminación del objeto:</span><span class="sxs-lookup"><span data-stu-id="d9020-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="d9020-327">Encapsular la matriz agrupada en un objeto desechable.</span><span class="sxs-lookup"><span data-stu-id="d9020-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="d9020-328">Registre el objeto agrupado con [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="d9020-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="d9020-329">`RegisterForDispose`se encargará `Dispose`de llamar al objeto de destino para que solo se libere cuando se complete la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d9020-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="d9020-330">La aplicación de la misma carga que la versión no agrupada da como resultado el siguiente gráfico:</span><span class="sxs-lookup"><span data-stu-id="d9020-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![gráfico anterior](memory/_static/pooledarray.png)

<span data-ttu-id="d9020-332">La diferencia principal es los bytes asignados y, como consecuencia, mucho menos las colecciones de generación 0.</span><span class="sxs-lookup"><span data-stu-id="d9020-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9020-333">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d9020-333">Additional resources</span></span>

* [<span data-ttu-id="d9020-334">Recolección de elementos no utilizados</span><span class="sxs-lookup"><span data-stu-id="d9020-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="d9020-335">Comprender los diferentes modos de GC con el visualizador de simultaneidad</span><span class="sxs-lookup"><span data-stu-id="d9020-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="d9020-336">Montón de objetos grandes descubiertos</span><span class="sxs-lookup"><span data-stu-id="d9020-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="d9020-337">Montón de objetos grandes</span><span class="sxs-lookup"><span data-stu-id="d9020-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
