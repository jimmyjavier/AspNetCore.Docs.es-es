---
title: Filtros en ASP.NET Core
author: Rick-Anderson
description: Obtenga información sobre cómo funcionan los filtros y cómo se pueden usar en ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 068b471c1f5fa5f0ca87dd7b028badf70f8c1b67
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122170"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="ba5ad-103">Filtros en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ba5ad-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba5ad-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ba5ad-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ba5ad-105">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ba5ad-106">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ba5ad-107">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ba5ad-108">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ba5ad-109">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ba5ad-110">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ba5ad-111">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="ba5ad-112">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ba5ad-113">Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="ba5ad-114">Los filtros no funcionan directamente con [ Razor los componentes](xref:blazor/components/index)de.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="ba5ad-115">Un filtro solo puede afectar indirectamente a un componente cuando:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="ba5ad-116">El componente está insertado en una página o vista.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="ba5ad-117">La página o el controlador o la vista utiliza el filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="ba5ad-118">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ba5ad-119">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="ba5ad-119">How filters work</span></span>

<span data-ttu-id="ba5ad-120">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="ba5ad-121">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases Otro middleware, Middleware de enrutamiento, Selección de acción y Canalización de invocación de acción.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ba5ad-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-124">Filter types</span></span>

<span data-ttu-id="ba5ad-125">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ba5ad-126">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ba5ad-127">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="ba5ad-128">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ba5ad-129">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-129">Run after authorization.</span></span>  
  * <span data-ttu-id="ba5ad-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> ejecuta código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ba5ad-131">Por ejemplo, `OnResourceExecuting` ejecuta código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="ba5ad-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ejecuta el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ba5ad-133">[Filtros de acción](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="ba5ad-134">Ejecutan código inmediatamente antes y después de llamar a un método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="ba5ad-135">Pueden cambiar los argumentos pasados a una acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="ba5ad-136">Pueden cambiar el resultado devuelto de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="ba5ad-137">**No** se admiten en Razor las páginas de.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ba5ad-138">Los [filtros de excepciones](#exception-filters) aplican directivas globales a las excepciones no controladas que se producen antes de que se escriba el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="ba5ad-139">Los [filtros de resultados](#result-filters) ejecutan código inmediatamente antes y después de la ejecución de resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="ba5ad-140">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ba5ad-141">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ba5ad-142">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ba5ad-145">Implementación</span><span class="sxs-lookup"><span data-stu-id="ba5ad-145">Implementation</span></span>

<span data-ttu-id="ba5ad-146">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ba5ad-147">Los filtros sincrónicos ejecutan código antes y después de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="ba5ad-148">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="ba5ad-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba5ad-150">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="ba5ad-151">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ba5ad-152">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ba5ad-153">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-153">Multiple filter stages</span></span>

<span data-ttu-id="ba5ad-154">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ba5ad-155">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="ba5ad-156">Sincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba5ad-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="ba5ad-157">Asincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba5ad-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="ba5ad-158">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ba5ad-159">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ba5ad-160">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ba5ad-161">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ba5ad-162">Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ba5ad-163">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="ba5ad-163">Built-in filter attributes</span></span>

<span data-ttu-id="ba5ad-164">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ba5ad-165">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ba5ad-166">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ba5ad-167">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="ba5ad-168">Use una herramienta como las [herramientas de desarrollo del explorador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="ba5ad-169">En **Encabezados de respuesta**, se muestra `author: Rick Anderson`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="ba5ad-170">El código siguiente implementa un atributo `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="ba5ad-171">Lee el título y el nombre del sistema de configuración.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="ba5ad-172">A diferencia del ejemplo anterior, el siguiente código no requiere que se agreguen parámetros de filtro al código.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="ba5ad-173">Agrega el título y el nombre al encabezado de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ba5ad-174">Las opciones de configuración las proporciona el [sistema de configuración](xref:fundamentals/configuration/index) mediante el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ba5ad-175">Por ejemplo, en el archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="ba5ad-176">En `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="ba5ad-177">La clase `PositionOptions` se agrega al contenedor de servicios con el área de configuración `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="ba5ad-178">`MyActionFilterAttribute` se agrega al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="ba5ad-179">En el código siguiente se muestra la clase `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="ba5ad-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="ba5ad-180">El siguiente código aplica el atributo `MyActionFilterAttribute` al método `Index2`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="ba5ad-181">En **encabezados de respuesta**, `author: Rick Anderson` y `Editor: Joe Smith` se muestra cuando `Sample/Index2` se llama al extremo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="ba5ad-182">El código siguiente aplica `MyActionFilterAttribute` y `AddHeaderAttribute` a la Razor página:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ba5ad-183">Los filtros no se pueden aplicar a Razor métodos de control de páginas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="ba5ad-184">Se pueden aplicar al Razor modelo de página o globalmente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="ba5ad-185">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ba5ad-186">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ba5ad-187">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="ba5ad-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="ba5ad-188">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ba5ad-189">Mediante un atributo en una acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="ba5ad-190">Los atributos de filtro no se pueden aplicar a Razor los métodos de control de páginas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="ba5ad-191">Usar un atributo en un controlador o una Razor página.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="ba5ad-192">Globalmente para todos los controladores, acciones y Razor páginas, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="ba5ad-193">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="ba5ad-193">Default order of execution</span></span>

<span data-ttu-id="ba5ad-194">Cuando hay varios filtros en una determinada fase de la canalización, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ba5ad-195">Los filtros globales abarcan a los filtros de clase, que a su vez engloban a los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="ba5ad-196">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ba5ad-197">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-197">The filter sequence:</span></span>

* <span data-ttu-id="ba5ad-198">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ba5ad-199">El código *anterior* de los filtros de controlador y de Razor página.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="ba5ad-200">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ba5ad-201">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ba5ad-202">*Después* del código de los filtros de controlador y de Razor página.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="ba5ad-203">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ba5ad-204">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ba5ad-205">Secuencia</span><span class="sxs-lookup"><span data-stu-id="ba5ad-205">Sequence</span></span> | <span data-ttu-id="ba5ad-206">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-206">Filter scope</span></span> | <span data-ttu-id="ba5ad-207">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ba5ad-208">1</span><span class="sxs-lookup"><span data-stu-id="ba5ad-208">1</span></span> | <span data-ttu-id="ba5ad-209">Global</span><span class="sxs-lookup"><span data-stu-id="ba5ad-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-210">2</span><span class="sxs-lookup"><span data-stu-id="ba5ad-210">2</span></span> | <span data-ttu-id="ba5ad-211">Controlador o Razor Página</span><span class="sxs-lookup"><span data-stu-id="ba5ad-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="ba5ad-212">3</span><span class="sxs-lookup"><span data-stu-id="ba5ad-212">3</span></span> | <span data-ttu-id="ba5ad-213">Método</span><span class="sxs-lookup"><span data-stu-id="ba5ad-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-214">4</span><span class="sxs-lookup"><span data-stu-id="ba5ad-214">4</span></span> | <span data-ttu-id="ba5ad-215">Método</span><span class="sxs-lookup"><span data-stu-id="ba5ad-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba5ad-216">5</span><span class="sxs-lookup"><span data-stu-id="ba5ad-216">5</span></span> | <span data-ttu-id="ba5ad-217">Controlador o Razor Página</span><span class="sxs-lookup"><span data-stu-id="ba5ad-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba5ad-218">6</span><span class="sxs-lookup"><span data-stu-id="ba5ad-218">6</span></span> | <span data-ttu-id="ba5ad-219">Global</span><span class="sxs-lookup"><span data-stu-id="ba5ad-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="ba5ad-220">Filtros de nivel de controlador</span><span class="sxs-lookup"><span data-stu-id="ba5ad-220">Controller level filters</span></span>

<span data-ttu-id="ba5ad-221">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ba5ad-222">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-222">These methods:</span></span>

* <span data-ttu-id="ba5ad-223">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ba5ad-224">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ba5ad-225">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ba5ad-226">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ba5ad-227">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ba5ad-228">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ba5ad-229">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-229">The `TestController`:</span></span>

* <span data-ttu-id="ba5ad-230">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="ba5ad-231">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="ba5ad-232">Si se dirige a `https://localhost:5001/Test2/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ba5ad-233">Los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="ba5ad-234">Los filtros de nivel de controlador **no** pueden establecerse para ejecutarse tras aplicarse los filtros a los métodos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="ba5ad-235">Order se explica en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-235">Order is explained in the next section.</span></span>

<span data-ttu-id="ba5ad-236">Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ba5ad-237">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="ba5ad-237">Overriding the default order</span></span>

<span data-ttu-id="ba5ad-238">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ba5ad-239"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ba5ad-240">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ba5ad-241">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ba5ad-242">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ba5ad-243">La propiedad `Order` se establece con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="ba5ad-244">Tenga en cuenta los dos filtros de acción en el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ba5ad-245">Un filtro global se agrega en `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="ba5ad-246">Los tres filtros se ejecutan en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="ba5ad-247">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ba5ad-248">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ba5ad-249">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ba5ad-250">Como se mencionó anteriormente, los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`. En el caso de los filtros integrados, el ámbito determina el orden a menos que se establezca `Order` en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="ba5ad-251">En el código anterior, `MySampleActionFilter` tiene ámbito global, por lo que se ejecuta antes de `MyAction2FilterAttribute`, que tiene ámbito de controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="ba5ad-252">Para que `MyAction2FilterAttribute` se ejecute en primer lugar, establezca el orden en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="ba5ad-253">Para que el filtro global `MySampleActionFilter` se ejecute en primer lugar, establezca `Order` en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ba5ad-254">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="ba5ad-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ba5ad-255">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ba5ad-256">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ba5ad-257">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ba5ad-258">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ba5ad-259">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ba5ad-260">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ba5ad-261">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ba5ad-262">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ba5ad-263">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="ba5ad-264">Inserción de dependencia</span><span class="sxs-lookup"><span data-stu-id="ba5ad-264">Dependency injection</span></span>

<span data-ttu-id="ba5ad-265">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ba5ad-266">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ba5ad-267">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ba5ad-268">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-268">A type-activated filter means:</span></span>

* <span data-ttu-id="ba5ad-269">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-269">An instance is created for each request.</span></span>
* <span data-ttu-id="ba5ad-270">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ba5ad-271">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ba5ad-272">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ba5ad-273">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ba5ad-274">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ba5ad-275">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ba5ad-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ba5ad-277">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ba5ad-278">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-278">Loggers are available from DI.</span></span> <span data-ttu-id="ba5ad-279">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ba5ad-280">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ba5ad-281">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-281">Logging added to filters:</span></span>

* <span data-ttu-id="ba5ad-282">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ba5ad-283">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ba5ad-284">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ba5ad-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ba5ad-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="ba5ad-286">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ba5ad-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ba5ad-288">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba5ad-289">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="ba5ad-290">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ba5ad-291">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ba5ad-292">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba5ad-293">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ba5ad-294">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ba5ad-295">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ba5ad-296">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ba5ad-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba5ad-298">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba5ad-299">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ba5ad-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ba5ad-300">TypeFilterAttribute</span></span>

<span data-ttu-id="ba5ad-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="ba5ad-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ba5ad-302">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ba5ad-303">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ba5ad-304">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ba5ad-305">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ba5ad-306">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ba5ad-307">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ba5ad-308">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba5ad-309">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ba5ad-310">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ba5ad-311">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ba5ad-312">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="ba5ad-312">Authorization filters</span></span>

<span data-ttu-id="ba5ad-313">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-313">Authorization filters:</span></span>

* <span data-ttu-id="ba5ad-314">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ba5ad-315">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-315">Control access to action methods.</span></span>
* <span data-ttu-id="ba5ad-316">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="ba5ad-317">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ba5ad-318">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ba5ad-319">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="ba5ad-320">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-320">Calls the authorization system.</span></span>
* <span data-ttu-id="ba5ad-321">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-321">Does not authorize requests.</span></span>

<span data-ttu-id="ba5ad-322">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ba5ad-323">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-323">The exception will not be handled.</span></span>
* <span data-ttu-id="ba5ad-324">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ba5ad-325">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ba5ad-326">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ba5ad-327">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="ba5ad-327">Resource filters</span></span>

<span data-ttu-id="ba5ad-328">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-328">Resource filters:</span></span>

* <span data-ttu-id="ba5ad-329">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ba5ad-330">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ba5ad-331">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ba5ad-332">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ba5ad-333">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ba5ad-334">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-334">Resource filter examples:</span></span>

* <span data-ttu-id="ba5ad-335">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ba5ad-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ba5ad-337">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ba5ad-338">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ba5ad-339">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="ba5ad-339">Action filters</span></span>

<span data-ttu-id="ba5ad-340">Los filtros de acción **no** se aplican a Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="ba5ad-341">Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba5ad-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ba5ad-342">Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ba5ad-343">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-343">Action filters:</span></span>

* <span data-ttu-id="ba5ad-344">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ba5ad-345">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ba5ad-346">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba5ad-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ba5ad-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="ba5ad-349"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ba5ad-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ba5ad-351">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ba5ad-352">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ba5ad-353">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba5ad-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ba5ad-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba5ad-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ba5ad-357">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-357">Setting this property to null:</span></span>

  * <span data-ttu-id="ba5ad-358">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ba5ad-359">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ba5ad-360">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ba5ad-361">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ba5ad-362">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ba5ad-363">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ba5ad-364">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ba5ad-365">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ba5ad-366">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-366">Validate model state.</span></span>
* <span data-ttu-id="ba5ad-367">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="ba5ad-368">Los controladores anotados con el `[ApiController]` atributo validan automáticamente el estado del modelo y devuelven una respuesta 400.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="ba5ad-369">Para obtener más información, consulte [Respuestas HTTP 400 automáticas](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="ba5ad-370">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ba5ad-371">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ba5ad-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba5ad-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ba5ad-374">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ba5ad-375">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ba5ad-376">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ba5ad-377">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="ba5ad-377">Exception filters</span></span>

<span data-ttu-id="ba5ad-378">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-378">Exception filters:</span></span>

* <span data-ttu-id="ba5ad-379">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="ba5ad-380">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ba5ad-381">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ba5ad-382">El código siguiente prueba el filtro de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="ba5ad-383">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-383">Exception filters:</span></span>

* <span data-ttu-id="ba5ad-384">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-384">Don't have before and after events.</span></span>
* <span data-ttu-id="ba5ad-385">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ba5ad-386">Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ba5ad-387">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ba5ad-388">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ba5ad-389">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-389">This stops propagation of the exception.</span></span> <span data-ttu-id="ba5ad-390">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="ba5ad-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ba5ad-391">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-391">Only an action filter can do that.</span></span>

<span data-ttu-id="ba5ad-392">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-392">Exception filters:</span></span>

* <span data-ttu-id="ba5ad-393">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ba5ad-394">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ba5ad-395">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ba5ad-396">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ba5ad-397">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ba5ad-398">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ba5ad-399">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="ba5ad-399">Result filters</span></span>

<span data-ttu-id="ba5ad-400">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-400">Result filters:</span></span>

* <span data-ttu-id="ba5ad-401">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-401">Implement an interface:</span></span>
  * <span data-ttu-id="ba5ad-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba5ad-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ba5ad-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba5ad-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ba5ad-404">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ba5ad-405">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba5ad-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ba5ad-406">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba5ad-407">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ba5ad-408">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ba5ad-409">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ba5ad-410">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ba5ad-411">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ba5ad-412">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="ba5ad-413">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ba5ad-414">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ba5ad-415">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ba5ad-416">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ba5ad-417">Generación de una excepción en `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="ba5ad-418">Impide la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ba5ad-419">Se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba5ad-420">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="ba5ad-421">En este caso, no se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="ba5ad-422">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ba5ad-423">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ba5ad-424">Establecer `Exception` en un valor null hace que una excepción se controle de forma eficaz y evita que se vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="ba5ad-425">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ba5ad-426">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ba5ad-427">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ba5ad-428">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ba5ad-429">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ba5ad-430">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ba5ad-431">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba5ad-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ba5ad-432">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ba5ad-433">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-433">This includes action results produced by:</span></span>

* <span data-ttu-id="ba5ad-434">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ba5ad-435">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-435">Exception filters.</span></span>

<span data-ttu-id="ba5ad-436">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ba5ad-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ba5ad-437">IFilterFactory</span></span>

<span data-ttu-id="ba5ad-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ba5ad-439">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ba5ad-440">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ba5ad-441">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ba5ad-442">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ba5ad-443">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ba5ad-444">El filtro se aplica en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="ba5ad-445">Pruebe el código anterior mediante la ejecución del [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="ba5ad-446">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ba5ad-447">Navegue a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="ba5ad-448">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ba5ad-449">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="ba5ad-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ba5ad-451">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-451">**internal:** `My header`</span></span>

<span data-ttu-id="ba5ad-452">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ba5ad-453">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="ba5ad-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ba5ad-454">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ba5ad-455">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="ba5ad-456">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ba5ad-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba5ad-458">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba5ad-459">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ba5ad-460">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ba5ad-461">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ba5ad-462">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ba5ad-463">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ba5ad-464">Pero los filtros se diferencian del middleware en que forman parte del runtime, lo que significa que tienen acceso al contexto y las construcciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="ba5ad-465">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ba5ad-466">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="ba5ad-467">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ba5ad-468">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ba5ad-469">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="ba5ad-469">Next actions</span></span>

* <span data-ttu-id="ba5ad-470">Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="ba5ad-471">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba5ad-472">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ba5ad-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ba5ad-473">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="ba5ad-474">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="ba5ad-475">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="ba5ad-476">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="ba5ad-477">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="ba5ad-478">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="ba5ad-479">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="ba5ad-480">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="ba5ad-481">Este documento se aplica a Razor las páginas, controladores de API y controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="ba5ad-482">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="ba5ad-483">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="ba5ad-483">How filters work</span></span>

<span data-ttu-id="ba5ad-484">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="ba5ad-485">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases de otro middleware, del middleware de enrutamiento, de la selección de acción y de la canalización de invocación de acciones de ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="ba5ad-488">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-488">Filter types</span></span>

<span data-ttu-id="ba5ad-489">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="ba5ad-490">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="ba5ad-491">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="ba5ad-492">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="ba5ad-493">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-493">Run after authorization.</span></span>  
  * <span data-ttu-id="ba5ad-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> puede ejecutar código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="ba5ad-495">Por ejemplo, `OnResourceExecuting` puede ejecutar código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="ba5ad-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> puede ejecutar el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="ba5ad-497">Los [filtros de acciones](#action-filters) pueden ejecutar código inmediatamente antes y después de llamar a un método de acción individual.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="ba5ad-498">Se pueden usar para manipular los argumentos pasados a una acción y el resultado obtenido de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="ba5ad-499">**No** se admiten filtros de acción en Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="ba5ad-500">Los [filtros de excepciones](#exception-filters) sirven para aplicar directivas globales a las excepciones no controladas que se producen antes de que se escriba algo en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="ba5ad-501">Los [filtros de resultados](#result-filters) pueden ejecutar código inmediatamente antes y después de la ejecución de resultados de acción individuales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="ba5ad-502">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="ba5ad-503">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="ba5ad-504">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="ba5ad-507">Implementación</span><span class="sxs-lookup"><span data-stu-id="ba5ad-507">Implementation</span></span>

<span data-ttu-id="ba5ad-508">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="ba5ad-509">Los filtros sincrónicos pueden ejecutar código antes (`On-Stage-Executing`) y después (`On-Stage-Executed`) de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="ba5ad-510">Por ejemplo, `OnActionExecuting` se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="ba5ad-511">`OnActionExecuted` se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba5ad-512">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="ba5ad-513">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="ba5ad-514">Cada uno de los métodos `On-Stage-ExecutionAsync` toman un `FilterType-ExecutionDelegate` que ejecuta la fase de canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="ba5ad-515">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-515">Multiple filter stages</span></span>

<span data-ttu-id="ba5ad-516">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="ba5ad-517">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` y sus equivalentes asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="ba5ad-518">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="ba5ad-519">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="ba5ad-520">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="ba5ad-521">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="ba5ad-522">Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="ba5ad-523">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="ba5ad-523">Built-in filter attributes</span></span>

<span data-ttu-id="ba5ad-524">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="ba5ad-525">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="ba5ad-526">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="ba5ad-527">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="ba5ad-528">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="ba5ad-529">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="ba5ad-530">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="ba5ad-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="ba5ad-531">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="ba5ad-532">Mediante un atributo en una acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="ba5ad-533">Mediante un atributo en un controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="ba5ad-534">Globalmente para todos los controladores y las acciones, tal como se muestra en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="ba5ad-535">El código anterior agrega tres filtros globalmente mediante la colección [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="ba5ad-536">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="ba5ad-536">Default order of execution</span></span>

<span data-ttu-id="ba5ad-537">Cuando hay varios filtros *del mismo tipo*, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="ba5ad-538">Los filtros globales delimitan los filtros de clase.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-538">Global filters surround class filters.</span></span> <span data-ttu-id="ba5ad-539">Los filtros de clase delimitan los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-539">Class filters surround method filters.</span></span>

<span data-ttu-id="ba5ad-540">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="ba5ad-541">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-541">The filter sequence:</span></span>

* <span data-ttu-id="ba5ad-542">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="ba5ad-543">El código *anterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="ba5ad-544">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="ba5ad-545">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="ba5ad-546">El código *posterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="ba5ad-547">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="ba5ad-548">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="ba5ad-549">Secuencia</span><span class="sxs-lookup"><span data-stu-id="ba5ad-549">Sequence</span></span> | <span data-ttu-id="ba5ad-550">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-550">Filter scope</span></span> | <span data-ttu-id="ba5ad-551">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="ba5ad-552">1</span><span class="sxs-lookup"><span data-stu-id="ba5ad-552">1</span></span> | <span data-ttu-id="ba5ad-553">Global</span><span class="sxs-lookup"><span data-stu-id="ba5ad-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-554">2</span><span class="sxs-lookup"><span data-stu-id="ba5ad-554">2</span></span> | <span data-ttu-id="ba5ad-555">Controller</span><span class="sxs-lookup"><span data-stu-id="ba5ad-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-556">3</span><span class="sxs-lookup"><span data-stu-id="ba5ad-556">3</span></span> | <span data-ttu-id="ba5ad-557">Método</span><span class="sxs-lookup"><span data-stu-id="ba5ad-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-558">4</span><span class="sxs-lookup"><span data-stu-id="ba5ad-558">4</span></span> | <span data-ttu-id="ba5ad-559">Método</span><span class="sxs-lookup"><span data-stu-id="ba5ad-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba5ad-560">5</span><span class="sxs-lookup"><span data-stu-id="ba5ad-560">5</span></span> | <span data-ttu-id="ba5ad-561">Controller</span><span class="sxs-lookup"><span data-stu-id="ba5ad-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="ba5ad-562">6</span><span class="sxs-lookup"><span data-stu-id="ba5ad-562">6</span></span> | <span data-ttu-id="ba5ad-563">Global</span><span class="sxs-lookup"><span data-stu-id="ba5ad-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="ba5ad-564">Esta secuencia pone de manifiesto lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-564">This sequence shows:</span></span>

* <span data-ttu-id="ba5ad-565">El filtro de método está anidado en el filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="ba5ad-566">El filtro de controlador está anidado en el filtro global.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="ba5ad-567">Filtros de Razor nivel de página y controlador</span><span class="sxs-lookup"><span data-stu-id="ba5ad-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="ba5ad-568">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="ba5ad-569">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-569">These methods:</span></span>

* <span data-ttu-id="ba5ad-570">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="ba5ad-571">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="ba5ad-572">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="ba5ad-573">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="ba5ad-574">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="ba5ad-575">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="ba5ad-576">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-576">The `TestController`:</span></span>

* <span data-ttu-id="ba5ad-577">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="ba5ad-578">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ba5ad-579">Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="ba5ad-580">Para Razor las páginas, consulte [implementar Razor filtros de página mediante la invalidación de métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="ba5ad-581">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="ba5ad-581">Overriding the default order</span></span>

<span data-ttu-id="ba5ad-582">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="ba5ad-583"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="ba5ad-584">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="ba5ad-585">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="ba5ad-586">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="ba5ad-587">La propiedad `Order` se puede establecer con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="ba5ad-588">Considere los mismos tres filtros de acción que se muestran en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="ba5ad-589">Si la propiedad `Order` del controlador y de los filtros globales está establecida en 1 y 2 respectivamente, el orden de ejecución se invierte.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="ba5ad-590">Secuencia</span><span class="sxs-lookup"><span data-stu-id="ba5ad-590">Sequence</span></span> | <span data-ttu-id="ba5ad-591">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-591">Filter scope</span></span> | <span data-ttu-id="ba5ad-592">Propiedad `Order`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-592">`Order` property</span></span> | <span data-ttu-id="ba5ad-593">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="ba5ad-594">1</span><span class="sxs-lookup"><span data-stu-id="ba5ad-594">1</span></span> | <span data-ttu-id="ba5ad-595">Método</span><span class="sxs-lookup"><span data-stu-id="ba5ad-595">Method</span></span> | <span data-ttu-id="ba5ad-596">0</span><span class="sxs-lookup"><span data-stu-id="ba5ad-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-597">2</span><span class="sxs-lookup"><span data-stu-id="ba5ad-597">2</span></span> | <span data-ttu-id="ba5ad-598">Controller</span><span class="sxs-lookup"><span data-stu-id="ba5ad-598">Controller</span></span> | <span data-ttu-id="ba5ad-599">1</span><span class="sxs-lookup"><span data-stu-id="ba5ad-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-600">3</span><span class="sxs-lookup"><span data-stu-id="ba5ad-600">3</span></span> | <span data-ttu-id="ba5ad-601">Global</span><span class="sxs-lookup"><span data-stu-id="ba5ad-601">Global</span></span> | <span data-ttu-id="ba5ad-602">2</span><span class="sxs-lookup"><span data-stu-id="ba5ad-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="ba5ad-603">4</span><span class="sxs-lookup"><span data-stu-id="ba5ad-603">4</span></span> | <span data-ttu-id="ba5ad-604">Global</span><span class="sxs-lookup"><span data-stu-id="ba5ad-604">Global</span></span> | <span data-ttu-id="ba5ad-605">2</span><span class="sxs-lookup"><span data-stu-id="ba5ad-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ba5ad-606">5</span><span class="sxs-lookup"><span data-stu-id="ba5ad-606">5</span></span> | <span data-ttu-id="ba5ad-607">Controller</span><span class="sxs-lookup"><span data-stu-id="ba5ad-607">Controller</span></span> | <span data-ttu-id="ba5ad-608">1</span><span class="sxs-lookup"><span data-stu-id="ba5ad-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="ba5ad-609">6</span><span class="sxs-lookup"><span data-stu-id="ba5ad-609">6</span></span> | <span data-ttu-id="ba5ad-610">Método</span><span class="sxs-lookup"><span data-stu-id="ba5ad-610">Method</span></span> | <span data-ttu-id="ba5ad-611">0</span><span class="sxs-lookup"><span data-stu-id="ba5ad-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="ba5ad-612">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="ba5ad-613">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="ba5ad-614">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="ba5ad-615">En los filtros integrados, el ámbito determina el orden, a menos que `Order` se establezca en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="ba5ad-616">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="ba5ad-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="ba5ad-617">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="ba5ad-618">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="ba5ad-619">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="ba5ad-620">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="ba5ad-621">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="ba5ad-622">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="ba5ad-623">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="ba5ad-624">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="ba5ad-625">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="ba5ad-626">Inserción de dependencia</span><span class="sxs-lookup"><span data-stu-id="ba5ad-626">Dependency injection</span></span>

<span data-ttu-id="ba5ad-627">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="ba5ad-628">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="ba5ad-629">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="ba5ad-630">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-630">A type-activated filter means:</span></span>

* <span data-ttu-id="ba5ad-631">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-631">An instance is created for each request.</span></span>
* <span data-ttu-id="ba5ad-632">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="ba5ad-633">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="ba5ad-634">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="ba5ad-635">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="ba5ad-636">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="ba5ad-637">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="ba5ad-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="ba5ad-639">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="ba5ad-640">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-640">Loggers are available from DI.</span></span> <span data-ttu-id="ba5ad-641">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="ba5ad-642">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="ba5ad-643">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-643">Logging added to filters:</span></span>

* <span data-ttu-id="ba5ad-644">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="ba5ad-645">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="ba5ad-646">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="ba5ad-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ba5ad-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="ba5ad-648">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="ba5ad-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="ba5ad-650">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba5ad-651">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="ba5ad-652">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="ba5ad-653">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="ba5ad-654">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba5ad-655">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="ba5ad-656">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="ba5ad-657">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="ba5ad-658">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="ba5ad-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba5ad-660">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba5ad-661">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="ba5ad-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="ba5ad-662">TypeFilterAttribute</span></span>

<span data-ttu-id="ba5ad-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="ba5ad-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="ba5ad-664">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="ba5ad-665">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="ba5ad-666">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="ba5ad-667">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="ba5ad-668">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="ba5ad-669">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="ba5ad-670">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="ba5ad-671">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="ba5ad-672">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="ba5ad-673">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="ba5ad-674">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="ba5ad-674">Authorization filters</span></span>

<span data-ttu-id="ba5ad-675">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-675">Authorization filters:</span></span>

* <span data-ttu-id="ba5ad-676">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="ba5ad-677">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-677">Control access to action methods.</span></span>
* <span data-ttu-id="ba5ad-678">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="ba5ad-679">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="ba5ad-680">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="ba5ad-681">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="ba5ad-682">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-682">Calls the authorization system.</span></span>
* <span data-ttu-id="ba5ad-683">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-683">Does not authorize requests.</span></span>

<span data-ttu-id="ba5ad-684">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="ba5ad-685">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-685">The exception will not be handled.</span></span>
* <span data-ttu-id="ba5ad-686">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="ba5ad-687">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="ba5ad-688">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="ba5ad-689">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="ba5ad-689">Resource filters</span></span>

<span data-ttu-id="ba5ad-690">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-690">Resource filters:</span></span>

* <span data-ttu-id="ba5ad-691">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="ba5ad-692">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="ba5ad-693">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="ba5ad-694">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="ba5ad-695">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="ba5ad-696">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-696">Resource filter examples:</span></span>

* <span data-ttu-id="ba5ad-697">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="ba5ad-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="ba5ad-699">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="ba5ad-700">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="ba5ad-701">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="ba5ad-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ba5ad-702">Los filtros de acción **no** se aplican a Razor las páginas.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="ba5ad-703">Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="ba5ad-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="ba5ad-704">Para obtener más información, vea [Métodos de filtrado de Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="ba5ad-705">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-705">Action filters:</span></span>

* <span data-ttu-id="ba5ad-706">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="ba5ad-707">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="ba5ad-708">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="ba5ad-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="ba5ad-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="ba5ad-711"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="ba5ad-712"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="ba5ad-713">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="ba5ad-714">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="ba5ad-715">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba5ad-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="ba5ad-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba5ad-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="ba5ad-719">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-719">Setting this property to null:</span></span>

  * <span data-ttu-id="ba5ad-720">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="ba5ad-721">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="ba5ad-722">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="ba5ad-723">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="ba5ad-724">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="ba5ad-725">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="ba5ad-726">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="ba5ad-727">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="ba5ad-728">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-728">Validate model state.</span></span>
* <span data-ttu-id="ba5ad-729">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="ba5ad-730">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="ba5ad-731">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="ba5ad-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="ba5ad-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="ba5ad-734">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="ba5ad-735">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="ba5ad-736">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="ba5ad-737">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="ba5ad-737">Exception filters</span></span>

<span data-ttu-id="ba5ad-738">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-738">Exception filters:</span></span>

* <span data-ttu-id="ba5ad-739">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="ba5ad-740">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="ba5ad-741">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="ba5ad-742">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-742">Exception filters:</span></span>

* <span data-ttu-id="ba5ad-743">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-743">Don't have before and after events.</span></span>
* <span data-ttu-id="ba5ad-744">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="ba5ad-745">Controlar las excepciones no controladas que se producen en la Razor creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="ba5ad-746">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="ba5ad-747">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="ba5ad-748">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-748">This stops propagation of the exception.</span></span> <span data-ttu-id="ba5ad-749">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="ba5ad-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="ba5ad-750">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-750">Only an action filter can do that.</span></span>

<span data-ttu-id="ba5ad-751">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-751">Exception filters:</span></span>

* <span data-ttu-id="ba5ad-752">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="ba5ad-753">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="ba5ad-754">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="ba5ad-755">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="ba5ad-756">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="ba5ad-757">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="ba5ad-758">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="ba5ad-758">Result filters</span></span>

<span data-ttu-id="ba5ad-759">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-759">Result filters:</span></span>

* <span data-ttu-id="ba5ad-760">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-760">Implement an interface:</span></span>
  * <span data-ttu-id="ba5ad-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba5ad-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="ba5ad-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="ba5ad-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="ba5ad-763">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="ba5ad-764">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba5ad-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="ba5ad-765">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="ba5ad-766">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="ba5ad-767">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="ba5ad-768">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="ba5ad-769">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="ba5ad-770">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="ba5ad-771">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="ba5ad-772">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="ba5ad-773">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="ba5ad-774">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="ba5ad-775">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="ba5ad-776">Si se inicia una excepción en `IResultFilter.OnResultExecuting`, sucederá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="ba5ad-777">Se impedirá la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="ba5ad-778">Se tratará como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="ba5ad-779">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="ba5ad-780">En este caso, no se puede cambiar más.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="ba5ad-781">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="ba5ad-782">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="ba5ad-783">Establecer `Exception` en un valor NULL hace que una excepción se "controle" de forma eficaz y evita que ASP.NET Core vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="ba5ad-784">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="ba5ad-785">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="ba5ad-786">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="ba5ad-787">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="ba5ad-788">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="ba5ad-789">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="ba5ad-790">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="ba5ad-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="ba5ad-791">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="ba5ad-792">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-792">This includes action results produced by:</span></span>

* <span data-ttu-id="ba5ad-793">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="ba5ad-794">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-794">Exception filters.</span></span>

<span data-ttu-id="ba5ad-795">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="ba5ad-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="ba5ad-796">IFilterFactory</span></span>

<span data-ttu-id="ba5ad-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="ba5ad-798">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="ba5ad-799">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="ba5ad-800">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="ba5ad-801">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="ba5ad-802">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="ba5ad-803">El código anterior se puede probar mediante la ejecución del [ejemplo de descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="ba5ad-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="ba5ad-804">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="ba5ad-805">Navegue a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="ba5ad-806">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="ba5ad-807">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="ba5ad-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="ba5ad-809">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="ba5ad-809">**internal:** `My header`</span></span>

<span data-ttu-id="ba5ad-810">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="ba5ad-811">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="ba5ad-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="ba5ad-812">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="ba5ad-813">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="ba5ad-814">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="ba5ad-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="ba5ad-816">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="ba5ad-817">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="ba5ad-818">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ba5ad-819">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="ba5ad-820">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="ba5ad-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="ba5ad-821">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="ba5ad-822">Pero los filtros se diferencian del middleware en que forman parte del entorno de ejecución de ASP.NET Core, lo que significa que tienen acceso al contexto y las construcciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="ba5ad-823">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="ba5ad-824">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="ba5ad-825">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="ba5ad-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="ba5ad-826">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="ba5ad-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="ba5ad-827">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="ba5ad-827">Next actions</span></span>

* <span data-ttu-id="ba5ad-828">Vea [métodos de filtro para Razor páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="ba5ad-829">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="ba5ad-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
