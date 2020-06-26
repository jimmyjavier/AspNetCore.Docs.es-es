---
title: Reutilización de objetos con ObjectPool en ASP.NET Core
author: rick-anderson
description: Sugerencias para aumentar el rendimiento en ASP.NET Core aplicaciones mediante ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 8244acb39a345875d80c5528a822de23f78b6e38
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403552"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="a7c2a-103">Reutilización de objetos con ObjectPool en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7c2a-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="a7c2a-104">Por [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7c2a-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a7c2a-105"><xref:Microsoft.Extensions.ObjectPool>forma parte de la infraestructura de ASP.NET Core que permite mantener un grupo de objetos en memoria para su reutilización, en lugar de permitir que los objetos se recopilen como elementos no utilizados.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="a7c2a-106">Es posible que desee usar el grupo de objetos si los objetos que se están administrando son:</span><span class="sxs-lookup"><span data-stu-id="a7c2a-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="a7c2a-107">Costosa asignación/inicialización.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="a7c2a-108">Representan algún recurso limitado.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-108">Represent some limited resource.</span></span>
- <span data-ttu-id="a7c2a-109">Se utiliza de forma predecible y frecuente.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-109">Used predictably and frequently.</span></span>

<span data-ttu-id="a7c2a-110">Por ejemplo, el marco de ASP.NET Core usa el grupo de objetos en algunos lugares para reutilizar <xref:System.Text.StringBuilder> las instancias.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="a7c2a-111">`StringBuilder`asigna y administra sus propios búferes para contener datos de caracteres.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="a7c2a-112">ASP.NET Core usa `StringBuilder` con regularidad para implementar características y su reutilización proporciona una ventaja de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="a7c2a-113">La agrupación de objetos no siempre mejora el rendimiento:</span><span class="sxs-lookup"><span data-stu-id="a7c2a-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="a7c2a-114">A menos que el costo de inicialización de un objeto sea alto, suele ser más lento obtener el objeto del grupo.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="a7c2a-115">No se anula la asignación de los objetos administrados por el grupo hasta que no se haya anulado la asignación del grupo.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="a7c2a-116">Use la agrupación de objetos solo después de recopilar datos de rendimiento mediante escenarios realistas para la aplicación o biblioteca.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="a7c2a-117">**ADVERTENCIA: `ObjectPool` no implementa `IDisposable` . No se recomienda su uso con tipos que necesitan la eliminación.**</span><span class="sxs-lookup"><span data-stu-id="a7c2a-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="a7c2a-118">**Nota: ObjectPool no impone ningún límite en cuanto al número de objetos que asignará, sino que establece un límite en el número de objetos que se conservarán.**</span><span class="sxs-lookup"><span data-stu-id="a7c2a-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="a7c2a-119">Conceptos</span><span class="sxs-lookup"><span data-stu-id="a7c2a-119">Concepts</span></span>

<span data-ttu-id="a7c2a-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-la abstracción básica del grupo de objetos.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="a7c2a-121">Se utiliza para obtener y devolver objetos.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-121">Used to get and return objects.</span></span>

<span data-ttu-id="a7c2a-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-implementar esto para personalizar cómo se crea un objeto y cómo se *restablece* cuando se devuelve al grupo.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="a7c2a-123">Se puede pasar a un grupo de objetos que se construye directamente.... DE</span><span class="sxs-lookup"><span data-stu-id="a7c2a-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="a7c2a-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>actúa como un generador para crear grupos de objetos.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="a7c2a-125">El ObjectPool se puede usar en una aplicación de varias maneras:</span><span class="sxs-lookup"><span data-stu-id="a7c2a-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="a7c2a-126">Crear una instancia de un grupo.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-126">Instantiating a pool.</span></span>
* <span data-ttu-id="a7c2a-127">Registrar un grupo en la [inserción de dependencias](xref:fundamentals/dependency-injection) (di) como una instancia de.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="a7c2a-128">Registrar `ObjectPoolProvider<>` en di y usarlo como generador.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="a7c2a-129">Cómo usar ObjectPool</span><span class="sxs-lookup"><span data-stu-id="a7c2a-129">How to use ObjectPool</span></span>

<span data-ttu-id="a7c2a-130">Llame <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> a para obtener un objeto y <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> devolver el objeto.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="a7c2a-131">No es necesario que devuelva todos los objetos.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="a7c2a-132">Si no devuelve un objeto, se recolectará como elemento no utilizado.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="a7c2a-133">Ejemplo de ObjectPool</span><span class="sxs-lookup"><span data-stu-id="a7c2a-133">ObjectPool sample</span></span>

<span data-ttu-id="a7c2a-134">El código siguiente:</span><span class="sxs-lookup"><span data-stu-id="a7c2a-134">The following code:</span></span>

* <span data-ttu-id="a7c2a-135">Agrega `ObjectPoolProvider` al contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a7c2a-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="a7c2a-136">Agrega y configura `ObjectPool<StringBuilder>` en el contenedor de di.</span><span class="sxs-lookup"><span data-stu-id="a7c2a-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="a7c2a-137">Agrega el `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="a7c2a-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="a7c2a-138">El código siguiente implementa`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="a7c2a-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
