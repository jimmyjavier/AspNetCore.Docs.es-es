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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>Administración de memoria y recolección de elementos no utilizados (GC) en ASP.NET Core

Por [Sébastien Ros](https://github.com/sebastienros) y [Rick Anderson](https://twitter.com/RickAndMSFT)

La administración de memoria es compleja, incluso en un marco administrado como .NET. Analizar y comprender los problemas de memoria puede ser un desafío. Este artículo:

* Fue motivado por muchas fugas de *memoria* y *GC no problemas de trabajo.* La mayoría de estos problemas se debieron a no comprender cómo funciona el consumo de memoria en .NET Core o a no comprender cómo se mide.
* Muestra el uso problemático de la memoria y sugiere enfoques alternativos.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>Cómo funciona la recolección de elementos no utilizados (GC) en .NET Core

El GC asigna segmentos de montón donde cada segmento es un rango contiguo de memoria. Los objetos colocados en el montón se clasifican en una de 3 generaciones: 0, 1 o 2. La generación determina la frecuencia con la que el GC intenta liberar memoria en objetos administrados a los que ya no hace referencia la aplicación. Las generaciones numeradas más bajas son GC'd con más frecuencia.

Los objetos se mueven de una generación a otra en función de su duración. A medida que los objetos viven más tiempo, se mueven a una generación superior. Como se mencionó anteriormente, las generaciones superiores son GC'd menos a menudo. Los objetos vividos a corto plazo siempre permanecen en la generación 0. Por ejemplo, los objetos a los que se hace referencia durante la vida de una solicitud web son de corta duración. Los [singletons](xref:fundamentals/dependency-injection#service-lifetimes) de nivel de aplicación generalmente migran a la generación 2.

Cuando se inicia una aplicación ASP.NET Core, el GC:

* Reserva algo de memoria para los segmentos de montón iniciales.
* Confirma una pequeña parte de la memoria cuando se carga el tiempo de ejecución.

Las asignaciones de memoria anteriores se realizan por motivos de rendimiento. La ventaja de rendimiento proviene de segmentos de montón en memoria contigua.

### <a name="call-gccollect"></a>Llama a GC. Recoger

Llamando a [GC. Recopilar](xref:System.GC.Collect*) explícitamente:

* **No** debe realizarse mediante la producción ASP.NET aplicaciones principales.
* Es útil cuando se investigan pérdidas de memoria.
* Al investigar, comprueba que el GC ha quitado todos los objetos colgantes de la memoria para que se pueda medir la memoria.

## <a name="analyzing-the-memory-usage-of-an-app"></a>Analizar el uso de memoria de una aplicación

Las herramientas dedicadas pueden ayudar a analizar el uso de memoria:

- Recuento de referencias a objetos
- Medición de cuánto impacto tiene el GC en el uso de la CPU
- Medición del espacio de memoria utilizado para cada generación

Utilice las siguientes herramientas para analizar el uso de memoria:

* [traza de puntos](/dotnet/core/diagnostics/dotnet-trace): Se puede utilizar en máquinas de producción.
* [Analizar el uso de memoria sin el depurador de Visual Studio](/visualstudio/profiling/memory-usage-without-debugging2)
* [Análisis del uso de memoria en Visual Studio](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>Detección de problemas de memoria

El Administrador de tareas se puede usar para hacerse una idea de la cantidad de memoria que usa una aplicación ASP.NET. El valor de memoria del Administrador de tareas:

* Representa la cantidad de memoria que usa el proceso de ASP.NET.
* Incluye los objetos vivos de la aplicación y otros consumidores de memoria, como el uso de memoria nativa.

Si el valor de memoria del Administrador de tareas aumenta indefinidamente y nunca se aplana, la aplicación tiene una pérdida de memoria. En las secciones siguientes se muestran y se explican varios patrones de uso de memoria.

## <a name="sample-display-memory-usage-app"></a>Ejemplo de aplicación de uso de memoria de pantalla

La aplicación de [ejemplo MemoryLeak](https://github.com/sebastienros/memoryleak) está disponible en GitHub. La aplicación MemoryLeak:

* Incluye un controlador de diagnóstico que recopila memoria en tiempo real y datos DE GC para la aplicación.
* Tiene una página de índice que muestra la memoria y los datos de GC. La página Index se actualiza cada segundo.
* Contiene un controlador de API que proporciona varios patrones de carga de memoria.
* No es una herramienta compatible, sin embargo, se puede utilizar para mostrar patrones de uso de memoria de ASP.NET aplicaciones principales.

Ejecute MemoryLeak. La memoria asignada aumenta lentamente hasta que se produce un GC. La memoria aumenta porque la herramienta asigna un objeto personalizado para capturar datos. La siguiente imagen muestra la página MemoryLeak Index cuando se produce un GC Gen 0. El gráfico muestra 0 RPS (solicitudes por segundo) porque no se ha llamado a ningún punto de conexión de API del controlador de API.

![gráfico anterior](memory/_static/0RPS.png)

El gráfico muestra dos valores para el uso de memoria:

- Asignado: la cantidad de memoria ocupada por objetos gestionados
- Conjunto de [trabajo:](/windows/win32/memory/working-set)el conjunto de páginas en el espacio de direcciones virtuales del proceso que residen actualmente en la memoria física. El conjunto de trabajo que se muestra es el mismo valor que muestra el Administrador de tareas.

### <a name="transient-objects"></a>Objetos transitorios

La siguiente API crea una instancia string de 10 KB y la devuelve al cliente. En cada solicitud, se asigna un nuevo objeto en la memoria y se escribe en la respuesta. Las cadenas se almacenan como caracteres UTF-16 en .NET, por lo que cada carácter toma 2 bytes en memoria.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

El siguiente gráfico se genera con una carga relativamente pequeña para mostrar cómo las asignaciones de memoria se ven afectadas por el GC.

![gráfico anterior](memory/_static/bigstring.png)

El gráfico anterior muestra:

* 4K RPS (Solicitudes por segundo).
* Las colecciones de GC de generación 0 se producen aproximadamente cada dos segundos.
* El conjunto de trabajo es constante en aproximadamente 500 MB.
* CPU es 12%.
* El consumo y la liberación de memoria (a través de GC) es estable.

El siguiente gráfico se toma al rendimiento máximo que puede controlar la máquina.

![gráfico anterior](memory/_static/bigstring2.png)

El gráfico anterior muestra:

* 22K RPS
* Las colecciones de GC de generación 0 se producen varias veces por segundo.
* Las colecciones de generación 1 se desencadenan porque la aplicación asignó significativamente más memoria por segundo.
* El conjunto de trabajo es constante en aproximadamente 500 MB.
* CPU es 33%.
* El consumo y la liberación de memoria (a través de GC) es estable.
* La CPU (33%) no se utiliza en exceso, por lo tanto, la recolección de elementos no utilizados puede mantenerse al día con un gran número de asignaciones.

### <a name="workstation-gc-vs-server-gc"></a>Workstation GC vs. Server GC

El recolector de elementos no utilizados de .NET tiene dos modos diferentes:

* **Workstation GC**: Optimizado para el escritorio.
* **Server GC**. El GC predeterminado para las aplicaciones ASP.NET Core. Optimizado para el servidor.

El modo GC se puede establecer explícitamente en el archivo de proyecto o en el archivo *runtimeconfig.json* de la aplicación publicada. El marcado siguiente `ServerGarbageCollection` muestra la configuración en el archivo de proyecto:

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

Cambiar `ServerGarbageCollection` en el archivo de proyecto requiere que la aplicación se vuelva a generar.

**Nota:** La recolección de elementos no utilizados del servidor **no** está disponible en equipos con un solo núcleo. Para obtener más información, vea <xref:System.Runtime.GCSettings.IsServerGC>.

La siguiente imagen muestra el perfil de memoria bajo un RPS 5K mediante la GC de la estación de trabajo.

![gráfico anterior](memory/_static/workstation.png)

Las diferencias entre este gráfico y la versión del servidor son significativas:

- El conjunto de trabajo cae de 500 MB a 70 MB.
- El GC genera 0 colecciones varias veces por segundo en lugar de cada dos segundos.
- GC cae de 300 MB a 10 MB.

En un entorno de servidor web típico, el uso de CPU es más importante que la memoria, por lo tanto, el GC del servidor es mejor. Si la utilización de memoria es alta y el uso de la CPU es relativamente bajo, el GC de la estación de trabajo pudo ser más eficaz. Por ejemplo, alta densidad alojando varias aplicaciones web donde la memoria es escasa.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>GC con Docker y contenedores pequeños

Cuando se ejecutan varias aplicaciones en contenedores en un equipo, Workstation GC puede ser más anterior que Server GC. Para obtener más información, consulte [Ejecución con GC de servidor en un contenedor pequeño](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) y Ejecución con GC de servidor en un escenario de contenedor pequeño Parte 1 – Límite duro para el [montón](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)de GC .

### <a name="persistent-object-references"></a>Referencias a objetos persistentes

El GC no puede liberar objetos a los que se hace referencia. Los objetos a los que se hace referencia pero que ya no son necesarios provocan una pérdida de memoria. Si la aplicación asigna objetos con frecuencia y no los libera después de que ya no se necesitan, el uso de memoria aumentará con el tiempo.

La siguiente API crea una instancia string de 10 KB y la devuelve al cliente. La diferencia con el ejemplo anterior es que un miembro estático hace referencia a esta instancia, lo que significa que nunca está disponible para su recopilación.

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

El código anterior:

* Es un ejemplo de una pérdida de memoria típica.
* Con las llamadas frecuentes, hace que la memoria `OutOfMemory` de la aplicación aumente hasta que el proceso se bloquee con una excepción.

![gráfico anterior](memory/_static/eternal.png)

En la imagen anterior:

* Las pruebas `/api/staticstring` de carga del punto de conexión provocan un aumento lineal de la memoria.
* El GC intenta liberar memoria a medida que crece la presión de memoria, llamando a una colección de generación 2.
* El GC no puede liberar la memoria filtrada. El conjunto asignado y de trabajo aumenta con el tiempo.

Algunos escenarios, como el almacenamiento en caché, requieren que se mantengan las referencias a objetos hasta que la presión de memoria obligue a liberarlas. La <xref:System.WeakReference> clase se puede utilizar para este tipo de código de almacenamiento en caché. Un `WeakReference` objeto se recopila bajo presiones de memoria. La implementación <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`predeterminada de uses .

### <a name="native-memory"></a>Memoria nativa

Algunos objetos de .NET Core dependen de la memoria nativa. La memoria nativa **no** puede ser recopilada por el GC. El objeto .NET con memoria nativa debe liberarlo mediante código nativo.

.NET proporciona <xref:System.IDisposable> la interfaz para permitir que los desarrolladores liberen memoria nativa. Incluso <xref:System.IDisposable.Dispose*> si no se llama, `Dispose` las clases implementadas correctamente llaman cuando se ejecuta el [finalizador.](/dotnet/csharp/programming-guide/classes-and-structs/destructors)

Observe el código siguiente:

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) es una clase administrada, por lo que cualquier instancia se recopilará al final de la solicitud.

La siguiente imagen muestra el perfil `fileprovider` de memoria al invocar la API continuamente.

![gráfico anterior](memory/_static/fileprovider.png)

El gráfico anterior muestra un problema obvio con la implementación de esta clase, ya que sigue aumentando el uso de memoria. Este es un problema conocido que se está rastreando en [este problema.](https://github.com/dotnet/aspnetcore/issues/3110)

La misma fuga podría ocurrir en el código de usuario, por uno de los siguientes:

* No liberar la clase correctamente.
* Olvidarse de `Dispose`invocar el método de los objetos dependientes que se deben eliminar.

### <a name="large-objects-heap"></a>Montón de objetos grandes

La asignación de memoria frecuente o los ciclos libres pueden fragmentar la memoria, especialmente cuando se asignan grandes fragmentos de memoria. Los objetos se asignan en bloques contiguos de memoria. Para mitigar la fragmentación, cuando el GC libera memoria, intenta desfragmentarla. Este proceso se denomina **compactación**. La compactación implica mover objetos. Mover objetos grandes impone una penalización de rendimiento. Por esta razón, el GC crea una zona de memoria especial para objetos _grandes,_ denominada montón de [objetos grandes](/dotnet/standard/garbage-collection/large-object-heap) (LOH). Los objetos que son mayores que 85.000 bytes (aproximadamente 83 KB) son:

* Colocado en la LOH.
* No compactado.
* Recolectado durante la generación de 2 GCs.

Cuando el LOH está lleno, el GC desencadenará una colección de generación 2. Colecciones de generación 2:

* Son intrínsecamente lentos.
* Además, incurrir en el costo de desencadenar una colección en todas las demás generaciones.

El código siguiente compacta el LOH inmediatamente:

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

Consulte <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> para obtener información sobre la compactación del LOH.

En los contenedores que usan .NET Core 3.0 y versiones posteriores, el LOH se compacta automáticamente.

La siguiente API que ilustra este comportamiento:

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

El siguiente gráfico muestra el `/api/loh/84975` perfil de memoria de llamar al punto de conexión, bajo carga máxima:

![gráfico anterior](memory/_static/loh1.png)

El siguiente gráfico muestra el `/api/loh/84976` perfil de memoria de llamar al punto de conexión, asignando *solo un byte más:*

![gráfico anterior](memory/_static/loh2.png)

Nota: `byte[]` La estructura tiene bytes de sobrecarga. Es por eso que 84.976 bytes desencadena el límite de 85.000.

Comparación de los dos gráficos anteriores:

* El conjunto de trabajo es similar para ambos escenarios, alrededor de 450 MB.
* Las solicitudes SUB LOH (84.975 bytes) muestran principalmente las colecciones de generación 0.
* Las solicitudes de sobre LOH generan colecciones de generación constante 2. Las colecciones de generación 2 son caras. Se requiere más CPU y el rendimiento disminuye casi un 50%.

Los objetos grandes temporales son particularmente problemáticos porque causan gén2 GCs.

Para obtener el máximo rendimiento, se debe minimizar el uso de objetos grandes. Si es posible, divida objetos grandes. Por ejemplo, el middleware [de almacenamiento en caché](xref:performance/caching/response) de respuestas en ASP.NET Core divide las entradas de caché en bloques de menos de 85.000 bytes.

Los vínculos siguientes muestran el enfoque ASP.NET Core para mantener los objetos por debajo del límite loH:

* [ResponseCaching/Streams/StreamUtilities.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [ResponseCaching/MemoryResponseCache.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

Para más información, consulte:

* [Montón de objetos grandes descubiertos](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Montón de objetos grandes](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

El <xref:System.Net.Http.HttpClient> uso incorrecto puede provocar una fuga de recursos. Recursos del sistema, como conexiones de base de datos, sockets, identificadores de archivos, etc.:

* Son más escasos que la memoria.
* Son más problemáticos cuando se pierden que la memoria.

Los desarrolladores experimentados <xref:System.IDisposable.Dispose*> de .NET <xref:System.IDisposable>saben llamar a objetos que implementan . No eliminar objetos `IDisposable` que implementan normalmente da como resultado memoria con pérdida o recursos del sistema filtrados.

`HttpClient`implementos `IDisposable`, pero **no** deben eliminarse en cada invocación. Más `HttpClient` bien, debe ser reutilizado.

El siguiente punto de conexión `HttpClient` crea y elimina una nueva instancia en cada solicitud:

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

En la carga, se registran los siguientes mensajes de error:

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

Aunque las `HttpClient` instancias se eliminan, la conexión de red real tarda algún tiempo en ser liberada por el sistema operativo. Al crear continuamente nuevas conexiones, se produce el agotamiento de _los puertos._ Cada conexión de cliente requiere su propio puerto de cliente.

Una manera de prevenir el agotamiento `HttpClient` del puerto es reutilizar la misma instancia:

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

La `HttpClient` instancia se libera cuando se detiene la aplicación. Este ejemplo muestra que no todos los recursos desechables deben eliminarse después de cada uso.

Consulte lo siguiente para obtener una mejor `HttpClient` manera de controlar la duración de una instancia:

* [HttpClient y administración de la duración](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [Blog de fábrica HTTPClient](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>Agrupación de objetos

En el ejemplo `HttpClient` anterior se muestra cómo se puede hacer estática y reutilizar la instancia por todas las solicitudes. La reutilización impide quedarse sin recursos.

Agrupación de objetos:

* Utiliza el patrón de reutilización.
* Está diseñado para objetos que son caros de crear.

Un grupo es una colección de objetos inicializados previamente que se pueden reservar y liberar entre subprocesos. Los grupos pueden definir reglas de asignación como límites, tamaños predefinidos o tasa de crecimiento.

El paquete NuGet [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contiene clases que ayudan a administrar dichos grupos.

El siguiente punto de `byte` conexión de API crea una instancia de un búfer que se rellena con números aleatorios en cada solicitud:

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

En la siguiente pantalla de gráfico se muestra la llamada a la API anterior con carga moderada:

![gráfico anterior](memory/_static/array.png)

En el gráfico anterior, las colecciones de generación 0 se producen aproximadamente una vez por segundo.

El código anterior se puede optimizar `byte` agrupando el búfer mediante [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1). Una instancia estática se reutiliza entre las solicitudes.

Lo que es diferente con este enfoque es que un objeto agrupado se devuelve desde la API. Eso significa:

* El objeto está fuera del control tan pronto como se vuelve del método.
* No se puede liberar el objeto.

Para configurar la eliminación del objeto:

* Encapsular la matriz agrupada en un objeto desechable.
* Registre el objeto agrupado con [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).

`RegisterForDispose`se encargará `Dispose`de llamar al objeto de destino para que solo se libere cuando se complete la solicitud HTTP.

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

La aplicación de la misma carga que la versión no agrupada da como resultado el siguiente gráfico:

![gráfico anterior](memory/_static/pooledarray.png)

La diferencia principal es los bytes asignados y, como consecuencia, mucho menos las colecciones de generación 0.

## <a name="additional-resources"></a>Recursos adicionales

* [Recolección de elementos no utilizados](/dotnet/standard/garbage-collection/)
* [Comprender los diferentes modos de GC con el visualizador de simultaneidad](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [Montón de objetos grandes descubiertos](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [Montón de objetos grandes](/dotnet/standard/garbage-collection/large-object-heap)
