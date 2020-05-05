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
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776791"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="e9ed6-103">Filtros en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9ed6-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9ed6-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e9ed6-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e9ed6-105">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="e9ed6-106">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="e9ed6-107">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="e9ed6-108">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="e9ed6-109">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="e9ed6-110">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="e9ed6-111">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="e9ed6-112">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="e9ed6-113">Este documento se aplica a Razor Pages, a los controladores de API y a los controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="e9ed6-114">Los filtros no funcionan directamente con [componentes de Razor](xref:blazor/components).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="e9ed6-115">Un filtro solo puede afectar indirectamente a un componente cuando:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="e9ed6-116">El componente está insertado en una página o vista.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="e9ed6-117">La página o el controlador o la vista utiliza el filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="e9ed6-118">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="e9ed6-119">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="e9ed6-119">How filters work</span></span>

<span data-ttu-id="e9ed6-120">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="e9ed6-121">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases Otro middleware, Middleware de enrutamiento, Selección de acción y Canalización de invocación de acción.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="e9ed6-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-124">Filter types</span></span>

<span data-ttu-id="e9ed6-125">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="e9ed6-126">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="e9ed6-127">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="e9ed6-128">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="e9ed6-129">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-129">Run after authorization.</span></span>  
  * <span data-ttu-id="e9ed6-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> ejecuta código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="e9ed6-131">Por ejemplo, `OnResourceExecuting` ejecuta código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="e9ed6-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> ejecuta el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="e9ed6-133">[Filtros de acción](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="e9ed6-134">Ejecutan código inmediatamente antes y después de llamar a un método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="e9ed6-135">Pueden cambiar los argumentos pasados a una acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="e9ed6-136">Pueden cambiar el resultado devuelto de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="e9ed6-137">**No** se admiten en Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="e9ed6-138">Los [filtros de excepciones](#exception-filters) aplican directivas globales a las excepciones no controladas que se producen antes de que se escriba el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="e9ed6-139">Los [filtros de resultados](#result-filters) ejecutan código inmediatamente antes y después de la ejecución de resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="e9ed6-140">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="e9ed6-141">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="e9ed6-142">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="e9ed6-145">Implementación</span><span class="sxs-lookup"><span data-stu-id="e9ed6-145">Implementation</span></span>

<span data-ttu-id="e9ed6-146">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="e9ed6-147">Los filtros sincrónicos ejecutan código antes y después de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="e9ed6-148">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="e9ed6-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e9ed6-150">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="e9ed6-151">Por ejemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="e9ed6-152">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="e9ed6-153">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-153">Multiple filter stages</span></span>

<span data-ttu-id="e9ed6-154">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="e9ed6-155">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="e9ed6-156">Sincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="e9ed6-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="e9ed6-157">Asincrónicas: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e9ed6-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="e9ed6-158">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e9ed6-159">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e9ed6-160">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e9ed6-161">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="e9ed6-162">Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="e9ed6-163">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="e9ed6-163">Built-in filter attributes</span></span>

<span data-ttu-id="e9ed6-164">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="e9ed6-165">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-166">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="e9ed6-167">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="e9ed6-168">Use una herramienta como las [herramientas de desarrollo del explorador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="e9ed6-169">En **encabezados de respuesta**, `author: Rick Anderson` se muestra.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="e9ed6-170">El código siguiente implementa un atributo `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="e9ed6-171">Lee el título y el nombre del sistema de configuración.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="e9ed6-172">A diferencia del ejemplo anterior, el siguiente código no requiere que se agreguen parámetros de filtro al código.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="e9ed6-173">Agrega el título y el nombre al encabezado de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-174">Las opciones de configuración las proporciona el [sistema de configuración](xref:fundamentals/configuration/index) mediante el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="e9ed6-175">Por ejemplo, en el archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="e9ed6-176">En `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="e9ed6-177">La clase `PositionOptions` se agrega al contenedor de servicios con el área de configuración `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="e9ed6-178">`MyActionFilterAttribute` se agrega al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="e9ed6-179">En el código siguiente se muestra la clase `PositionOptions` :</span><span class="sxs-lookup"><span data-stu-id="e9ed6-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="e9ed6-180">El siguiente código aplica el atributo `MyActionFilterAttribute` al método `Index2`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="e9ed6-181">En **encabezados de respuesta**, `author: Rick Anderson`y `Editor: Joe Smith` se muestra cuando se llama `Sample/Index2` al extremo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="e9ed6-182">El siguiente código aplica los atributos `MyActionFilterAttribute` y `AddHeaderAttribute` a la página de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e9ed6-183">Los filtros no se pueden usar con métodos de controlador de páginas de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="e9ed6-184">Se pueden usar con el modelo de página de Razor Pages o globalmente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="e9ed6-185">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="e9ed6-186">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="e9ed6-187">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="e9ed6-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="e9ed6-188">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="e9ed6-189">Mediante un atributo en una acción del controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="e9ed6-190">Los atributos de filtro no se pueden usar con métodos de controlador de páginas de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="e9ed6-191">Mediante un atributo en un controlador o una página de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="e9ed6-192">Globalmente para todos los controladores, las acciones y las páginas de Razor Pages tal como se muestra en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="e9ed6-193">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="e9ed6-193">Default order of execution</span></span>

<span data-ttu-id="e9ed6-194">Cuando hay varios filtros en una determinada fase de la canalización, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="e9ed6-195">Los filtros globales abarcan a los filtros de clase, que a su vez engloban a los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="e9ed6-196">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="e9ed6-197">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-197">The filter sequence:</span></span>

* <span data-ttu-id="e9ed6-198">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="e9ed6-199">El código *anterior* de los filtros de controlador y página de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="e9ed6-200">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="e9ed6-201">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="e9ed6-202">El código *posterior* de los filtros de controlador y página de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="e9ed6-203">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="e9ed6-204">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="e9ed6-205">Secuencia</span><span class="sxs-lookup"><span data-stu-id="e9ed6-205">Sequence</span></span> | <span data-ttu-id="e9ed6-206">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-206">Filter scope</span></span> | <span data-ttu-id="e9ed6-207">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="e9ed6-208">1</span><span class="sxs-lookup"><span data-stu-id="e9ed6-208">1</span></span> | <span data-ttu-id="e9ed6-209">Global</span><span class="sxs-lookup"><span data-stu-id="e9ed6-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-210">2</span><span class="sxs-lookup"><span data-stu-id="e9ed6-210">2</span></span> | <span data-ttu-id="e9ed6-211">Controlador o página de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e9ed6-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="e9ed6-212">3</span><span class="sxs-lookup"><span data-stu-id="e9ed6-212">3</span></span> | <span data-ttu-id="e9ed6-213">Método</span><span class="sxs-lookup"><span data-stu-id="e9ed6-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-214">4</span><span class="sxs-lookup"><span data-stu-id="e9ed6-214">4</span></span> | <span data-ttu-id="e9ed6-215">Método</span><span class="sxs-lookup"><span data-stu-id="e9ed6-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e9ed6-216">5</span><span class="sxs-lookup"><span data-stu-id="e9ed6-216">5</span></span> | <span data-ttu-id="e9ed6-217">Controlador o página de Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e9ed6-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e9ed6-218">6</span><span class="sxs-lookup"><span data-stu-id="e9ed6-218">6</span></span> | <span data-ttu-id="e9ed6-219">Global</span><span class="sxs-lookup"><span data-stu-id="e9ed6-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="e9ed6-220">Filtros de nivel de controlador</span><span class="sxs-lookup"><span data-stu-id="e9ed6-220">Controller level filters</span></span>

<span data-ttu-id="e9ed6-221">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="e9ed6-222">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-222">These methods:</span></span>

* <span data-ttu-id="e9ed6-223">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="e9ed6-224">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="e9ed6-225">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="e9ed6-226">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="e9ed6-227">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="e9ed6-228">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="e9ed6-229">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-229">The `TestController`:</span></span>

* <span data-ttu-id="e9ed6-230">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="e9ed6-231">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="e9ed6-232">Si se dirige a `https://localhost:5001/Test2/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="e9ed6-233">Los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="e9ed6-234">Los filtros de nivel de controlador **no** pueden establecerse para ejecutarse tras aplicarse los filtros a los métodos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="e9ed6-235">Order se explica en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-235">Order is explained in the next section.</span></span>

<span data-ttu-id="e9ed6-236">Para Razor Pages, consulte [Implementar filtros de páginas de Razor globalmente](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="e9ed6-237">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="e9ed6-237">Overriding the default order</span></span>

<span data-ttu-id="e9ed6-238">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="e9ed6-239"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="e9ed6-240">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="e9ed6-241">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="e9ed6-242">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="e9ed6-243">La propiedad `Order` se establece con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="e9ed6-244">Tenga en cuenta los dos filtros de acción en el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="e9ed6-245">Un filtro global se agrega en `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="e9ed6-246">Los tres filtros se ejecutan en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="e9ed6-247">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="e9ed6-248">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="e9ed6-249">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="e9ed6-250">Como se mencionó anteriormente, los filtros de nivel de controlador establecen la propiedad [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) en `int.MinValue`. En el caso de los filtros integrados, el ámbito determina el orden a menos que se establezca `Order` en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="e9ed6-251">En el código anterior, `MySampleActionFilter` tiene ámbito global, por lo que se ejecuta antes de `MyAction2FilterAttribute`, que tiene ámbito de controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="e9ed6-252">Para que `MyAction2FilterAttribute` se ejecute en primer lugar, establezca el orden en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="e9ed6-253">Para que el filtro global `MySampleActionFilter` se ejecute en primer lugar, establezca `Order` en `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="e9ed6-254">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="e9ed6-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="e9ed6-255">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="e9ed6-256">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-257">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="e9ed6-258">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="e9ed6-259">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="e9ed6-260">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="e9ed6-261">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="e9ed6-262">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="e9ed6-263">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="e9ed6-264">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="e9ed6-264">Dependency injection</span></span>

<span data-ttu-id="e9ed6-265">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="e9ed6-266">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="e9ed6-267">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="e9ed6-268">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-268">A type-activated filter means:</span></span>

* <span data-ttu-id="e9ed6-269">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-269">An instance is created for each request.</span></span>
* <span data-ttu-id="e9ed6-270">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="e9ed6-271">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e9ed6-272">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="e9ed6-273">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="e9ed6-274">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="e9ed6-275">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="e9ed6-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="e9ed6-277">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="e9ed6-278">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-278">Loggers are available from DI.</span></span> <span data-ttu-id="e9ed6-279">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="e9ed6-280">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="e9ed6-281">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-281">Logging added to filters:</span></span>

* <span data-ttu-id="e9ed6-282">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="e9ed6-283">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="e9ed6-284">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="e9ed6-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e9ed6-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="e9ed6-286">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="e9ed6-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="e9ed6-288">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e9ed6-289">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="e9ed6-290">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="e9ed6-291">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="e9ed6-292">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e9ed6-293">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="e9ed6-294">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="e9ed6-295">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="e9ed6-296">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="e9ed6-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e9ed6-298">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e9ed6-299">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="e9ed6-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e9ed6-300">TypeFilterAttribute</span></span>

<span data-ttu-id="e9ed6-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="e9ed6-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="e9ed6-302">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="e9ed6-303">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="e9ed6-304">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="e9ed6-305">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="e9ed6-306">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="e9ed6-307">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="e9ed6-308">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e9ed6-309">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="e9ed6-310">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="e9ed6-311">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="e9ed6-312">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="e9ed6-312">Authorization filters</span></span>

<span data-ttu-id="e9ed6-313">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-313">Authorization filters:</span></span>

* <span data-ttu-id="e9ed6-314">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="e9ed6-315">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-315">Control access to action methods.</span></span>
* <span data-ttu-id="e9ed6-316">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="e9ed6-317">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="e9ed6-318">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="e9ed6-319">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="e9ed6-320">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-320">Calls the authorization system.</span></span>
* <span data-ttu-id="e9ed6-321">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-321">Does not authorize requests.</span></span>

<span data-ttu-id="e9ed6-322">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="e9ed6-323">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-323">The exception will not be handled.</span></span>
* <span data-ttu-id="e9ed6-324">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="e9ed6-325">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="e9ed6-326">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="e9ed6-327">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="e9ed6-327">Resource filters</span></span>

<span data-ttu-id="e9ed6-328">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-328">Resource filters:</span></span>

* <span data-ttu-id="e9ed6-329">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="e9ed6-330">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="e9ed6-331">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="e9ed6-332">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="e9ed6-333">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="e9ed6-334">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-334">Resource filter examples:</span></span>

* <span data-ttu-id="e9ed6-335">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="e9ed6-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="e9ed6-337">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="e9ed6-338">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="e9ed6-339">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="e9ed6-339">Action filters</span></span>

<span data-ttu-id="e9ed6-340">Los filtros de acción **no** se aplican a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="e9ed6-341">Razor Pages admite <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> y <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="e9ed6-342">Para más información, vea [Filter methods for Razor Pages](xref:razor-pages/filter) (Métodos de filtrado para páginas de Razor).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="e9ed6-343">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-343">Action filters:</span></span>

* <span data-ttu-id="e9ed6-344">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="e9ed6-345">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="e9ed6-346">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e9ed6-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="e9ed6-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="e9ed6-349"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="e9ed6-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="e9ed6-351">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="e9ed6-352">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="e9ed6-353">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e9ed6-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="e9ed6-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e9ed6-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="e9ed6-357">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-357">Setting this property to null:</span></span>

  * <span data-ttu-id="e9ed6-358">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="e9ed6-359">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="e9ed6-360">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="e9ed6-361">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="e9ed6-362">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="e9ed6-363">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="e9ed6-364">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="e9ed6-365">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="e9ed6-366">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-366">Validate model state.</span></span>
* <span data-ttu-id="e9ed6-367">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-368">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="e9ed6-369">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="e9ed6-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e9ed6-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="e9ed6-372">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="e9ed6-373">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="e9ed6-374">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="e9ed6-375">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="e9ed6-375">Exception filters</span></span>

<span data-ttu-id="e9ed6-376">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-376">Exception filters:</span></span>

* <span data-ttu-id="e9ed6-377">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="e9ed6-378">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="e9ed6-379">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="e9ed6-380">El código siguiente prueba el filtro de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="e9ed6-381">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-381">Exception filters:</span></span>

* <span data-ttu-id="e9ed6-382">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-382">Don't have before and after events.</span></span>
* <span data-ttu-id="e9ed6-383">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="e9ed6-384">Controlan las excepciones sin controlar que se producen en Razor Pages, al crear controladores, en el [enlace de modelos](xref:mvc/models/model-binding), en los filtros de acciones o en los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="e9ed6-385">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="e9ed6-386">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="e9ed6-387">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-387">This stops propagation of the exception.</span></span> <span data-ttu-id="e9ed6-388">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="e9ed6-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="e9ed6-389">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-389">Only an action filter can do that.</span></span>

<span data-ttu-id="e9ed6-390">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-390">Exception filters:</span></span>

* <span data-ttu-id="e9ed6-391">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="e9ed6-392">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="e9ed6-393">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="e9ed6-394">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="e9ed6-395">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="e9ed6-396">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="e9ed6-397">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="e9ed6-397">Result filters</span></span>

<span data-ttu-id="e9ed6-398">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-398">Result filters:</span></span>

* <span data-ttu-id="e9ed6-399">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-399">Implement an interface:</span></span>
  * <span data-ttu-id="e9ed6-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e9ed6-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="e9ed6-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="e9ed6-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="e9ed6-402">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="e9ed6-403">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="e9ed6-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="e9ed6-404">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e9ed6-405">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="e9ed6-406">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="e9ed6-407">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="e9ed6-408">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="e9ed6-409">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="e9ed6-410">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="e9ed6-411">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="e9ed6-412">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="e9ed6-413">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="e9ed6-414">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="e9ed6-415">Generación de una excepción en `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="e9ed6-416">Impide la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="e9ed6-417">Se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e9ed6-418">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="e9ed6-419">En este caso, no se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="e9ed6-420">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="e9ed6-421">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="e9ed6-422">Establecer `Exception` en un valor null hace que una excepción se controle de forma eficaz y evita que se vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="e9ed6-423">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="e9ed6-424">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="e9ed6-425">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="e9ed6-426">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="e9ed6-427">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="e9ed6-428">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="e9ed6-429">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="e9ed6-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="e9ed6-430">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="e9ed6-431">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-431">This includes action results produced by:</span></span>

* <span data-ttu-id="e9ed6-432">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="e9ed6-433">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-433">Exception filters.</span></span>

<span data-ttu-id="e9ed6-434">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="e9ed6-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="e9ed6-435">IFilterFactory</span></span>

<span data-ttu-id="e9ed6-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="e9ed6-437">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="e9ed6-438">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="e9ed6-439">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="e9ed6-440">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="e9ed6-441">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="e9ed6-442">El filtro se aplica en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="e9ed6-443">Pruebe el código anterior mediante la ejecución del [ejemplo de descarga](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="e9ed6-444">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="e9ed6-445">Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="e9ed6-446">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="e9ed6-447">**autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="e9ed6-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="e9ed6-449">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-449">**internal:** `My header`</span></span>

<span data-ttu-id="e9ed6-450">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="e9ed6-451">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="e9ed6-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="e9ed6-452">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="e9ed6-453">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="e9ed6-454">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="e9ed6-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e9ed6-456">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e9ed6-457">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="e9ed6-458">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e9ed6-459">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="e9ed6-460">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="e9ed6-461">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="e9ed6-462">Pero los filtros se diferencian del middleware en que forman parte del runtime, lo que significa que tienen acceso al contexto y las construcciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="e9ed6-463">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="e9ed6-464">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="e9ed6-465">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="e9ed6-466">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="e9ed6-467">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="e9ed6-467">Next actions</span></span>

* <span data-ttu-id="e9ed6-468">Vea [métodos de filtro para obtener Razor pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="e9ed6-469">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9ed6-470">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e9ed6-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e9ed6-471">Los *filtros* en ASP.NET Core permiten que se ejecute el código antes o después de determinadas fases de la canalización del procesamiento de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="e9ed6-472">Los filtros integrados se encargan de tareas como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="e9ed6-473">Autorización (impedir el acceso a los recursos a un usuario que no está autorizado).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="e9ed6-474">Almacenamiento en caché de respuestas (cortocircuitar la canalización de solicitud para devolver una respuesta almacenada en caché).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="e9ed6-475">Se pueden crear filtros personalizados que se encarguen de cuestiones transversales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="e9ed6-476">Entre los ejemplos de cuestiones transversales se incluyen el control de errores, el almacenamiento en caché, la configuración, la autorización y el registro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="e9ed6-477">Los filtros evitan la duplicación de código.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="e9ed6-478">Así, por ejemplo, un filtro de excepción de control de errores puede consolidar el control de errores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="e9ed6-479">Este documento se aplica a Razor Pages, a los controladores de API y a los controladores con vistas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="e9ed6-480">[Vea o descargue el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="e9ed6-481">Funcionamiento de los filtros</span><span class="sxs-lookup"><span data-stu-id="e9ed6-481">How filters work</span></span>

<span data-ttu-id="e9ed6-482">Los filtros se ejecutan dentro de la *canalización de invocación de acciones de ASP.NET Core*, a veces denominada *canalización de filtro*.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="e9ed6-483">La canalización de filtro se ejecuta después de que ASP.NET Core seleccione la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La solicitud se procesa a través de las fases de otro middleware, del middleware de enrutamiento, de la selección de acción y de la canalización de invocación de acciones de ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="e9ed6-486">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-486">Filter types</span></span>

<span data-ttu-id="e9ed6-487">Cada tipo de filtro se ejecuta en una fase diferente dentro de la canalización de filtro:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="e9ed6-488">Los [filtros de autorización](#authorization-filters) se ejecutan en primer lugar y sirven para averiguar si el usuario está autorizado para realizar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="e9ed6-489">Los filtros de autorización pueden cortocircuitar la canalización si una solicitud no está autorizada.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="e9ed6-490">[Filtros de recursos](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="e9ed6-491">Se ejecutan después de la autorización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-491">Run after authorization.</span></span>  
  * <span data-ttu-id="e9ed6-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> puede ejecutar código antes que el resto de la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="e9ed6-493">Por ejemplo, `OnResourceExecuting` puede ejecutar código antes que el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="e9ed6-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> puede ejecutar el código una vez que el resto de la canalización se haya completado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="e9ed6-495">Los [filtros de acciones](#action-filters) pueden ejecutar código inmediatamente antes y después de llamar a un método de acción individual.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="e9ed6-496">Se pueden usar para manipular los argumentos pasados a una acción y el resultado obtenido de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="e9ed6-497">Los filtros de acciones **no** se admiten en Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="e9ed6-498">Los [filtros de excepciones](#exception-filters) sirven para aplicar directivas globales a las excepciones no controladas que se producen antes de que se escriba algo en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="e9ed6-499">Los [filtros de resultados](#result-filters) pueden ejecutar código inmediatamente antes y después de la ejecución de resultados de acción individuales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="e9ed6-500">Se ejecutan solo cuando el método de acción se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="e9ed6-501">Son útiles para la lógica que debe regir la ejecución de la vista o el formateador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="e9ed6-502">En el siguiente diagrama se muestra cómo interactúan los tipos de filtro en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La solicitud se procesa a través de las fases Filtros de autorización, Filtros de recursos, Enlace de modelos, Filtros de acciones, Ejecución de acciones/Conversión del resultado de acción, Filtros de excepción, Filtros de resultados y Ejecución del resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="e9ed6-505">Implementación</span><span class="sxs-lookup"><span data-stu-id="e9ed6-505">Implementation</span></span>

<span data-ttu-id="e9ed6-506">Los filtros admiten implementaciones tanto sincrónicas como asincrónicas a través de diferentes definiciones de interfaz.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="e9ed6-507">Los filtros sincrónicos pueden ejecutar código antes (`On-Stage-Executing`) y después (`On-Stage-Executed`) de la fase de canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="e9ed6-508">Por ejemplo, `OnActionExecuting` se llama antes de llamar al método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="e9ed6-509">`OnActionExecuted` se llama después de devolver el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e9ed6-510">Los filtros asincrónicos definen un método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="e9ed6-511">En el código anterior, `SampleAsyncActionFilter` tiene un delegado <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que ejecuta el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="e9ed6-512">Cada uno de los métodos `On-Stage-ExecutionAsync` toman un `FilterType-ExecutionDelegate` que ejecuta la fase de canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="e9ed6-513">Varias fases de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-513">Multiple filter stages</span></span>

<span data-ttu-id="e9ed6-514">Se pueden implementar interfaces para varias fases de filtro en una sola clase.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="e9ed6-515">Por ejemplo, la clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` y sus equivalentes asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="e9ed6-516">Implemente la versión sincrónica **o** la versión asincrónica de una interfaz de filtro, pero **no** ambas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="e9ed6-517">El entorno de ejecución comprueba primero si el filtro implementa la interfaz asincrónica y, si es así, llama a la interfaz.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="e9ed6-518">De lo contrario, llamará a métodos de interfaz sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="e9ed6-519">Si se implementan las interfaces asincrónicas y sincrónicas en una clase, solo se llama al método asincrónico.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="e9ed6-520">Cuando se usan clases abstractas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, se invalidan solo los métodos sincrónicos o el método asincrónico de cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="e9ed6-521">Atributos de filtros integrados</span><span class="sxs-lookup"><span data-stu-id="e9ed6-521">Built-in filter attributes</span></span>

<span data-ttu-id="e9ed6-522">ASP.NET Core incluye filtros integrados basados en atributos que se pueden personalizar y a partir de los cuales crear subclases.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="e9ed6-523">Por ejemplo, el siguiente filtro de resultados agrega un encabezado a la respuesta:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-524">Los atributos permiten a los filtros aceptar argumentos, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="e9ed6-525">Aplique el `AddHeaderAttribute` a un método de acción o controlador y especifique el nombre y el valor del encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="e9ed6-526">Algunas de las interfaces de filtro tienen atributos correspondientes que se pueden usar como clases base en las implementaciones personalizadas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="e9ed6-527">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="e9ed6-528">Ámbitos del filtro y orden de ejecución</span><span class="sxs-lookup"><span data-stu-id="e9ed6-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="e9ed6-529">Un filtro se puede agregar a la canalización en uno de tres *ámbitos* posibles:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="e9ed6-530">Mediante un atributo en una acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="e9ed6-531">Mediante un atributo en un controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="e9ed6-532">Globalmente para todos los controladores y las acciones, tal como se muestra en el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="e9ed6-533">El código anterior agrega tres filtros globalmente mediante la colección [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="e9ed6-534">Orden de ejecución predeterminado</span><span class="sxs-lookup"><span data-stu-id="e9ed6-534">Default order of execution</span></span>

<span data-ttu-id="e9ed6-535">Cuando hay varios filtros *del mismo tipo*, el ámbito determina el orden predeterminado en el que esos filtros se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="e9ed6-536">Los filtros globales delimitan los filtros de clase.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-536">Global filters surround class filters.</span></span> <span data-ttu-id="e9ed6-537">Los filtros de clase delimitan los filtros de método.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-537">Class filters surround method filters.</span></span>

<span data-ttu-id="e9ed6-538">Como resultado de este anidamiento de filtros, el código de filtros *posterior* se ejecuta en el orden inverso al código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="e9ed6-539">La secuencia de filtro:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-539">The filter sequence:</span></span>

* <span data-ttu-id="e9ed6-540">El código *anterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="e9ed6-541">El código *anterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="e9ed6-542">El código *anterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="e9ed6-543">El código *posterior* de los filtros de métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="e9ed6-544">El código *posterior* de los filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="e9ed6-545">El código *posterior* de los filtros globales.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="e9ed6-546">El ejemplo siguiente que ilustra el orden en el que se llama a los métodos de filtro relativos a filtros de acciones sincrónicos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="e9ed6-547">Secuencia</span><span class="sxs-lookup"><span data-stu-id="e9ed6-547">Sequence</span></span> | <span data-ttu-id="e9ed6-548">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-548">Filter scope</span></span> | <span data-ttu-id="e9ed6-549">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="e9ed6-550">1</span><span class="sxs-lookup"><span data-stu-id="e9ed6-550">1</span></span> | <span data-ttu-id="e9ed6-551">Global</span><span class="sxs-lookup"><span data-stu-id="e9ed6-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-552">2</span><span class="sxs-lookup"><span data-stu-id="e9ed6-552">2</span></span> | <span data-ttu-id="e9ed6-553">Controller</span><span class="sxs-lookup"><span data-stu-id="e9ed6-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-554">3</span><span class="sxs-lookup"><span data-stu-id="e9ed6-554">3</span></span> | <span data-ttu-id="e9ed6-555">Método</span><span class="sxs-lookup"><span data-stu-id="e9ed6-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-556">4</span><span class="sxs-lookup"><span data-stu-id="e9ed6-556">4</span></span> | <span data-ttu-id="e9ed6-557">Método</span><span class="sxs-lookup"><span data-stu-id="e9ed6-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e9ed6-558">5</span><span class="sxs-lookup"><span data-stu-id="e9ed6-558">5</span></span> | <span data-ttu-id="e9ed6-559">Controller</span><span class="sxs-lookup"><span data-stu-id="e9ed6-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="e9ed6-560">6</span><span class="sxs-lookup"><span data-stu-id="e9ed6-560">6</span></span> | <span data-ttu-id="e9ed6-561">Global</span><span class="sxs-lookup"><span data-stu-id="e9ed6-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="e9ed6-562">Esta secuencia pone de manifiesto lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-562">This sequence shows:</span></span>

* <span data-ttu-id="e9ed6-563">El filtro de método está anidado en el filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="e9ed6-564">El filtro de controlador está anidado en el filtro global.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="e9ed6-565">Filtros de nivel de controlador y de páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="e9ed6-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="e9ed6-566">Cada controlador que hereda de la clase base <xref:Microsoft.AspNetCore.Mvc.Controller> incluye los métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) y [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="e9ed6-567">Estos métodos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-567">These methods:</span></span>

* <span data-ttu-id="e9ed6-568">Encapsulan los filtros que se ejecutan para una acción determinada.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="e9ed6-569">`OnActionExecuting` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="e9ed6-570">`OnActionExecuted` se llama después de todos los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="e9ed6-571">`OnActionExecutionAsync` se llama antes de cualquiera de los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="e9ed6-572">El código del filtro después de `next` se ejecuta después del método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="e9ed6-573">Por ejemplo, en el ejemplo de descarga, se aplica `MySampleActionFilter` globalmente al inicio.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="e9ed6-574">El `TestController`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-574">The `TestController`:</span></span>

* <span data-ttu-id="e9ed6-575">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) a la acción `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="e9ed6-576">Invalida `OnActionExecuting` y `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="e9ed6-577">Si se dirige a `https://localhost:5001/Test/FilterTest2`, se ejecuta el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="e9ed6-578">Para Razor Pages, consulte [Implementar filtros de páginas de Razor globalmente](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="e9ed6-579">Invalidación del orden predeterminado</span><span class="sxs-lookup"><span data-stu-id="e9ed6-579">Overriding the default order</span></span>

<span data-ttu-id="e9ed6-580">La secuencia de ejecución predeterminada se puede invalidar con la implementación de <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="e9ed6-581"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> expone la propiedad `IOrderedFilter` que tiene prioridad sobre el ámbito a la hora de determinar el orden de ejecución.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="e9ed6-582">Un filtro con un valor `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="e9ed6-583">Ejecuta el código *anterior* antes que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="e9ed6-584">Ejecuta el código *posterior* después que el de un filtro con un valor mayor de `Order`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="e9ed6-585">La propiedad `Order` se puede establecer con un parámetro de constructor:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="e9ed6-586">Considere los mismos tres filtros de acción que se muestran en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="e9ed6-587">Si la propiedad `Order` del controlador y de los filtros globales está establecida en 1 y 2 respectivamente, el orden de ejecución se invierte.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="e9ed6-588">Secuencia</span><span class="sxs-lookup"><span data-stu-id="e9ed6-588">Sequence</span></span> | <span data-ttu-id="e9ed6-589">Ámbito del filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-589">Filter scope</span></span> | <span data-ttu-id="e9ed6-590">Propiedad`Order`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-590">`Order` property</span></span> | <span data-ttu-id="e9ed6-591">Método de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="e9ed6-592">1</span><span class="sxs-lookup"><span data-stu-id="e9ed6-592">1</span></span> | <span data-ttu-id="e9ed6-593">Método</span><span class="sxs-lookup"><span data-stu-id="e9ed6-593">Method</span></span> | <span data-ttu-id="e9ed6-594">0</span><span class="sxs-lookup"><span data-stu-id="e9ed6-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-595">2</span><span class="sxs-lookup"><span data-stu-id="e9ed6-595">2</span></span> | <span data-ttu-id="e9ed6-596">Controller</span><span class="sxs-lookup"><span data-stu-id="e9ed6-596">Controller</span></span> | <span data-ttu-id="e9ed6-597">1</span><span class="sxs-lookup"><span data-stu-id="e9ed6-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-598">3</span><span class="sxs-lookup"><span data-stu-id="e9ed6-598">3</span></span> | <span data-ttu-id="e9ed6-599">Global</span><span class="sxs-lookup"><span data-stu-id="e9ed6-599">Global</span></span> | <span data-ttu-id="e9ed6-600">2</span><span class="sxs-lookup"><span data-stu-id="e9ed6-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="e9ed6-601">4</span><span class="sxs-lookup"><span data-stu-id="e9ed6-601">4</span></span> | <span data-ttu-id="e9ed6-602">Global</span><span class="sxs-lookup"><span data-stu-id="e9ed6-602">Global</span></span> | <span data-ttu-id="e9ed6-603">2</span><span class="sxs-lookup"><span data-stu-id="e9ed6-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="e9ed6-604">5</span><span class="sxs-lookup"><span data-stu-id="e9ed6-604">5</span></span> | <span data-ttu-id="e9ed6-605">Controller</span><span class="sxs-lookup"><span data-stu-id="e9ed6-605">Controller</span></span> | <span data-ttu-id="e9ed6-606">1</span><span class="sxs-lookup"><span data-stu-id="e9ed6-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="e9ed6-607">6</span><span class="sxs-lookup"><span data-stu-id="e9ed6-607">6</span></span> | <span data-ttu-id="e9ed6-608">Método</span><span class="sxs-lookup"><span data-stu-id="e9ed6-608">Method</span></span> | <span data-ttu-id="e9ed6-609">0</span><span class="sxs-lookup"><span data-stu-id="e9ed6-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="e9ed6-610">La propiedad `Order` invalida el ámbito al determinar el orden en el que se ejecutarán los filtros.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="e9ed6-611">Los filtros se clasifican por orden en primer lugar y, después, se usa el ámbito para priorizar en caso de igualdad.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="e9ed6-612">Todos los filtros integrados implementan `IOrderedFilter` y establecen el valor predeterminado de `Order` en 0.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="e9ed6-613">En los filtros integrados, el ámbito determina el orden, a menos que `Order` se establezca en un valor distinto de cero.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="e9ed6-614">Cancelación y cortocircuito</span><span class="sxs-lookup"><span data-stu-id="e9ed6-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="e9ed6-615">La canalización de filtro se puede cortocircuitar en cualquier momento mediante el establecimiento de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> en el parámetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> que se ha proporcionado al método de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="e9ed6-616">Por ejemplo, el siguiente filtro de recursos impide que el resto de la canalización se ejecute:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-617">En el siguiente código, tanto el filtro `ShortCircuitingResourceFilter` como el filtro `AddHeader` tienen como destino el método de acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="e9ed6-618">El `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="e9ed6-619">Se ejecuta en primer lugar, porque es un filtro de recursos y `AddHeader` es un filtro de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="e9ed6-620">Cortocircuita el resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="e9ed6-621">Por tanto, el filtro `AddHeader` nunca se ejecuta en relación con la acción `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="e9ed6-622">Este comportamiento sería el mismo si ambos filtros se aplicaran en el nivel de método de acción, siempre y cuando `ShortCircuitingResourceFilter` se haya ejecutado primero.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="e9ed6-623">`ShortCircuitingResourceFilter` se ejecuta primero debido a su tipo de filtro o al uso explícito de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="e9ed6-624">Inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="e9ed6-624">Dependency injection</span></span>

<span data-ttu-id="e9ed6-625">Los filtros se pueden agregar por tipo o por instancia.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="e9ed6-626">Si se agrega una instancia, esta se utiliza para todas las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="e9ed6-627">Si se agrega un tipo, se activa por tipo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="e9ed6-628">Un filtro activado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-628">A type-activated filter means:</span></span>

* <span data-ttu-id="e9ed6-629">Se crea una instancia para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-629">An instance is created for each request.</span></span>
* <span data-ttu-id="e9ed6-630">Las dependencias de constructor se rellenan mediante la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="e9ed6-631">Los filtros que se implementan como atributos y se agregan directamente a las clases de controlador o a los métodos de acción no pueden tener dependencias de constructor proporcionadas por la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="e9ed6-632">Las dependencias de constructor no pueden proporcionarse mediante la inserción de dependencias porque:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="e9ed6-633">Los atributos deben tener los parámetros de constructor proporcionados allá donde se apliquen.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="e9ed6-634">Se trata de una limitación de cómo funcionan los atributos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="e9ed6-635">Los filtros siguientes admiten dependencias de constructor proporcionadas en la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="e9ed6-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> se implementa en el atributo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="e9ed6-637">Los filtros anteriores se pueden aplicar a un método de controlador o de acción:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="e9ed6-638">Los registradores están disponibles en la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-638">Loggers are available from DI.</span></span> <span data-ttu-id="e9ed6-639">Sin embargo, evite crear y utilizar filtros únicamente con fines de registro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="e9ed6-640">El [registro del marco integrado](xref:fundamentals/logging/index) proporciona normalmente lo que se necesita para el registro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="e9ed6-641">Registro agregado a los filtros:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-641">Logging added to filters:</span></span>

* <span data-ttu-id="e9ed6-642">Debe centrarse en cuestiones de dominio empresarial o en el comportamiento específico del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="e9ed6-643">**No** debe registrar acciones u otros eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="e9ed6-644">Los filtros integrados registran acciones y eventos del marco.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="e9ed6-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e9ed6-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="e9ed6-646">Los tipos de implementación de filtro de servicio se registran en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="e9ed6-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera una instancia del filtro de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="e9ed6-648">El código siguiente muestra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e9ed6-649">En el código siguiente, `AddHeaderResultServiceFilter` se agrega al contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="e9ed6-650">En el código siguiente, el atributo `ServiceFilter` recupera una instancia del filtro `AddHeaderResultServiceFilter` desde la inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="e9ed6-651">Al usar `ServiceFilterAttribute`, si se establece [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="e9ed6-652">Proporciona una sugerencia que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e9ed6-653">El entorno de ejecución de ASP.NET Core no garantiza:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="e9ed6-654">Que se creará una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="e9ed6-655">El filtro no volverá a solicitarse desde el contenedor de inserción de dependencias en algún momento posterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="e9ed6-656">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="e9ed6-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e9ed6-658">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e9ed6-659">`CreateInstance` carga el tipo especificado desde la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="e9ed6-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="e9ed6-660">TypeFilterAttribute</span></span>

<span data-ttu-id="e9ed6-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> es similar a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, pero su tipo no se resuelve directamente desde el contenedor de inserción de dependencias,</span><span class="sxs-lookup"><span data-stu-id="e9ed6-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="e9ed6-662">sino que crea una instancia del tipo usando el elemento <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="e9ed6-663">Dado que los tipos `TypeFilterAttribute` no se resuelven directamente desde el contenedor de inserción de dependencias:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="e9ed6-664">Los tipos a los que se hace referencia con `TypeFilterAttribute` no tienen que estar registrados con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="e9ed6-665">Sus dependencias se completan a través del contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="e9ed6-666">`TypeFilterAttribute` puede aceptar opcionalmente argumentos de constructor del tipo en cuestión.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="e9ed6-667">Al usar `TypeFilterAttribute`, si se establece [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="e9ed6-668">Proporciona una sugerencia que indica que la instancia de filtro *podría* reutilizarse fuera del ámbito de la solicitud en la que se creó.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="e9ed6-669">El entorno de ejecución de ASP.NET Core no ofrece ninguna garantía de que se vaya a crear una única instancia del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="e9ed6-670">No debe usarse con un filtro que depende de servicios con una duración distinta de singleton.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="e9ed6-671">En el siguiente ejemplo se muestra cómo pasar argumentos a un tipo mediante `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="e9ed6-672">Filtros de autorización</span><span class="sxs-lookup"><span data-stu-id="e9ed6-672">Authorization filters</span></span>

<span data-ttu-id="e9ed6-673">Filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-673">Authorization filters:</span></span>

* <span data-ttu-id="e9ed6-674">Son los primeros filtros que se ejecutan en la canalización del filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="e9ed6-675">Controlan el acceso a los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-675">Control access to action methods.</span></span>
* <span data-ttu-id="e9ed6-676">Tienen un método anterior, pero no uno posterior.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="e9ed6-677">Los filtros de autorización personalizados requieren un marco de autorización personalizado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="e9ed6-678">Es preferible configurar directivas de autorización o escribir una directiva de autorización personalizada a escribir un filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="e9ed6-679">El filtro de autorización integrado:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="e9ed6-680">Llama a la autorización del sistema.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-680">Calls the authorization system.</span></span>
* <span data-ttu-id="e9ed6-681">No autoriza las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-681">Does not authorize requests.</span></span>

<span data-ttu-id="e9ed6-682">**No** inicie excepciones dentro de los filtros de autorización:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="e9ed6-683">La excepción no se controlará.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-683">The exception will not be handled.</span></span>
* <span data-ttu-id="e9ed6-684">Los filtros de excepciones no controlarán la excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="e9ed6-685">Considere la posibilidad de emitir un desafío cuando se produzca una excepción en un filtro de autorizaciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="e9ed6-686">[Aquí](xref:security/authorization/introduction) encontrará más información sobre la autorización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="e9ed6-687">Filtros de recursos</span><span class="sxs-lookup"><span data-stu-id="e9ed6-687">Resource filters</span></span>

<span data-ttu-id="e9ed6-688">Filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-688">Resource filters:</span></span>

* <span data-ttu-id="e9ed6-689">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="e9ed6-690">La ejecución encapsula la mayor parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="e9ed6-691">Los [filtros de autorizaciones](#authorization-filters) son los únicos que se ejecutan antes que los filtros de recursos.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="e9ed6-692">Los filtros de recursos son útiles para cortocircuitar la mayor parte de la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="e9ed6-693">Por ejemplo, un filtro de almacenamiento en caché puede evitar que se ejecute el resto de la canalización en un acierto de caché.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="e9ed6-694">Ejemplos de filtros de recursos:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-694">Resource filter examples:</span></span>

* <span data-ttu-id="e9ed6-695">[El filtro de recursos de cortocircuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="e9ed6-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="e9ed6-697">Evita que el enlace de modelos tenga acceso a los datos del formulario.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="e9ed6-698">Se utiliza cuando hay cargas de archivos muy voluminosos para impedir que los datos del formulario se lean en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="e9ed6-699">Filtros de acciones</span><span class="sxs-lookup"><span data-stu-id="e9ed6-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e9ed6-700">Los filtros de acción **no** se Razor aplican a las páginas.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="e9ed6-701">Pages <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> admite <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> y.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="e9ed6-702">Para obtener más información, vea [métodos de Razor filtro para páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="e9ed6-703">Filtros de acciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-703">Action filters:</span></span>

* <span data-ttu-id="e9ed6-704">Implementan la interfaz <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="e9ed6-705">Su ejecución rodea la ejecución de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="e9ed6-706">El código siguiente muestra un ejemplo de filtro de acciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="e9ed6-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> ofrece las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="e9ed6-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: permite leer las entradas de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="e9ed6-709"><xref:Microsoft.AspNetCore.Mvc.Controller>: permite manipular la instancia del controlador.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="e9ed6-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>: si se establece `Result`, se cortocircuita la ejecución del método de acción y de los filtros de acciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="e9ed6-711">Inicio de una excepción en un método de acción:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="e9ed6-712">Impide la ejecución de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="e9ed6-713">A diferencia del establecimiento de `Result`, se trata como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e9ed6-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> proporciona `Controller` y `Result`, además de las siguientes propiedades:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="e9ed6-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: es true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e9ed6-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: es un valor distinto de NULL si la acción o un filtro de acción de ejecución anterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="e9ed6-717">Si se establece esta propiedad en un valor NULL:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-717">Setting this property to null:</span></span>

  * <span data-ttu-id="e9ed6-718">Controla la excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="e9ed6-719">`Result` se ejecuta como si se devolviera desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="e9ed6-720">En un `IAsyncActionFilter`, una llamada a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="e9ed6-721">Ejecuta cualquier filtro de acciones posterior y el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="e9ed6-722">Devuelve `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="e9ed6-723">Para cortocircuitar esto, asigne <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a una instancia de resultado y no llame a `next` (la clase `ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="e9ed6-724">El marco proporciona una clase <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="e9ed6-725">Se puede usar el filtro de acción `OnActionExecuting` para:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="e9ed6-726">Validar el estado del modelo.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-726">Validate model state.</span></span>
* <span data-ttu-id="e9ed6-727">Devolver un error si el estado no es válido.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="e9ed6-728">El método `OnActionExecuted` se ejecuta después del método de acción:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="e9ed6-729">Y puede ver y manipular los resultados de la acción a través de la propiedad <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="e9ed6-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> se establece en true si otro filtro ha cortocircuitado la ejecución de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="e9ed6-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> se establece en un valor distinto de NULL si la acción o un filtro de acción posterior han producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="e9ed6-732">Si `Exception` se establece como nulo:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="e9ed6-733">Controla una excepción eficazmente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="e9ed6-734">`ActionExecutedContext.Result` se ejecuta como si se devolviera con normalidad desde el método de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="e9ed6-735">Filtros de excepciones</span><span class="sxs-lookup"><span data-stu-id="e9ed6-735">Exception filters</span></span>

<span data-ttu-id="e9ed6-736">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-736">Exception filters:</span></span>

* <span data-ttu-id="e9ed6-737">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="e9ed6-738">Se pueden usar para implementar directivas de control de errores comunes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="e9ed6-739">En el siguiente filtro de excepciones de ejemplo se usa una vista de error personalizada para mostrar los detalles sobre las excepciones que se producen cuando la aplicación está en fase de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="e9ed6-740">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-740">Exception filters:</span></span>

* <span data-ttu-id="e9ed6-741">No tienen eventos anteriores ni posteriores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-741">Don't have before and after events.</span></span>
* <span data-ttu-id="e9ed6-742">Implementan <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="e9ed6-743">Controlar las excepciones no controladas que se Razor producen en la creación de páginas o controladores, [enlace de modelos](xref:mvc/models/model-binding), filtros de acción o métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="e9ed6-744">**No** detectan aquellas excepciones que se produzcan en los filtros de recursos, en los filtros de resultados o en la ejecución de resultados de MVC.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="e9ed6-745">Para controlar una excepción, establezca la propiedad <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> en `true` o escriba una respuesta.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="e9ed6-746">Esto detiene la propagación de la excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-746">This stops propagation of the exception.</span></span> <span data-ttu-id="e9ed6-747">Un filtro de excepciones no tiene capacidad para convertir una excepción en un proceso "correcto".</span><span class="sxs-lookup"><span data-stu-id="e9ed6-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="e9ed6-748">Eso solo lo pueden hacer los filtros de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-748">Only an action filter can do that.</span></span>

<span data-ttu-id="e9ed6-749">Los filtros de excepciones:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-749">Exception filters:</span></span>

* <span data-ttu-id="e9ed6-750">Son adecuados para interceptar las excepciones que se producen en las acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="e9ed6-751">No son tan flexibles como el middleware de control de errores.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="e9ed6-752">Es preferible usar middleware de control de excepciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="e9ed6-753">Utilice los filtros de excepciones solo cuando el control de errores *es diferente* en función del método de acción que se llama.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="e9ed6-754">Por ejemplo, puede que una aplicación tenga métodos de acción tanto para los puntos de conexión de API como para las vistas/HTML.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="e9ed6-755">Los puntos de conexión de API podrían devolver información sobre errores como JSON, mientras que las acciones basadas en vistas podrían devolver una página de error como HTML.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="e9ed6-756">Filtros de resultados</span><span class="sxs-lookup"><span data-stu-id="e9ed6-756">Result filters</span></span>

<span data-ttu-id="e9ed6-757">Filtros de resultados:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-757">Result filters:</span></span>

* <span data-ttu-id="e9ed6-758">Implementar una interfaz:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-758">Implement an interface:</span></span>
  * <span data-ttu-id="e9ed6-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="e9ed6-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="e9ed6-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="e9ed6-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="e9ed6-761">Su ejecución rodea la ejecución de los resultados de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="e9ed6-762">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="e9ed6-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="e9ed6-763">El código siguiente muestra un filtro de resultados que agrega un encabezado HTTP:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="e9ed6-764">El tipo de resultado que se ejecute dependerá de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="e9ed6-765">Una acción que devuelve una vista incluye todo el procesamiento de Razor como parte del elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> que se está ejecutando.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="e9ed6-766">Un método API puede llevar a cabo algunas funciones de serialización como parte de la ejecución del resultado.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="e9ed6-767">[Aquí](xref:mvc/controllers/actions) encontrará más información sobre los resultados de acciones.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="e9ed6-768">Los filtros de resultados solo se ejecutan cuando una acción o un filtro de acción genera un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="e9ed6-769">Los filtros de resultados no se ejecutan cuando:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="e9ed6-770">Un filtro de autorización o un filtro de recursos genera un cortocircuito en la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="e9ed6-771">Un filtro de excepciones controla una excepción al producir un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="e9ed6-772">El método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> puede cortocircuitar la ejecución del resultado de la acción y de los filtros de resultados posteriores mediante el establecimiento de <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> en `true`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="e9ed6-773">Escriba en el objeto de respuesta cuando el proceso se cortocircuite, ya que así evitará que se genere una respuesta vacía.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="e9ed6-774">Si se inicia una excepción en `IResultFilter.OnResultExecuting`, sucederá lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="e9ed6-775">Se impedirá la ejecución del resultado de la acción y de los filtros subsiguientes.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="e9ed6-776">Se tratará como un error en lugar de como un resultado correcto.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="e9ed6-777">Cuando se ejecuta el método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, es probable que la respuesta ya se haya enviado al cliente.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="e9ed6-778">En este caso, no se puede cambiar más.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="e9ed6-779">`ResultExecutedContext.Canceled` se establece en `true` si otro filtro ha cortocircuitado la ejecución del resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="e9ed6-780">`ResultExecutedContext.Exception` se establece en un valor distinto de NULL si el resultado de la acción o un filtro de resultado posterior ha producido una excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="e9ed6-781">Establecer `Exception` en un valor NULL hace que una excepción se "controle" de forma eficaz y evita que ASP.NET Core vuelva a producir dicha excepción más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="e9ed6-782">No hay una forma confiable de escribir datos en una respuesta cuando se controla una excepción en un filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="e9ed6-783">Si los encabezados ya se han vaciado en el cliente si el resultado de una acción inicia una excepción, no hay ningún mecanismo confiable que permita enviar un código de error.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="e9ed6-784">En un elemento <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una llamada a `await next` en <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> ejecuta cualquier filtro de resultados posterior y el resultado de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="e9ed6-785">Para cortocircuitarlo, establezca [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) en `true` y no llame a `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="e9ed6-786">El marco proporciona una clase `ResultFilterAttribute` abstracta de la que se pueden crear subclases.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="e9ed6-787">La clase [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente es un ejemplo de un atributo de filtro de resultados.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="e9ed6-788">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="e9ed6-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="e9ed6-789">Las interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaran una implementación <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que se ejecuta para obtener todos los resultados de la acción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="e9ed6-790">Esto incluye los resultados de la acción generados por:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-790">This includes action results produced by:</span></span>

* <span data-ttu-id="e9ed6-791">Filtros de autorización y filtros de recursos que generan un cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="e9ed6-792">Filtros de excepción.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-792">Exception filters.</span></span>

<span data-ttu-id="e9ed6-793">Por ejemplo, el siguiente filtro siempre ejecuta y establece un resultado de la acción (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un código de estado *422 - Entidad no procesable* cuando se produce un error en la negociación de contenido:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="e9ed6-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="e9ed6-794">IFilterFactory</span></span>

<span data-ttu-id="e9ed6-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="e9ed6-796">Por tanto, una instancia de `IFilterFactory` se puede usar como una instancia de `IFilterMetadata` en cualquier parte de la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="e9ed6-797">Cuando el entorno de ejecución se prepara para invocar el filtro, intenta convertirlo a un `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="e9ed6-798">Si esa conversión se realiza correctamente, se llama al método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear la instancia `IFilterMetadata` que se va a invocar.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="e9ed6-799">Esto proporciona un diseño flexible, dado que no hay que establecer la canalización de filtro exacta de forma explícita cuando la aplicación se inicia.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="e9ed6-800">Puede implementar `IFilterFactory` con las implementaciones de atributos personalizados como método alternativo para crear filtros:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="e9ed6-801">El código anterior se puede probar mediante la ejecución del [ejemplo de descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="e9ed6-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="e9ed6-802">Invoque las herramientas de desarrollador de F12.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="e9ed6-803">Vaya a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="e9ed6-804">Las herramientas de desarrollador F12 muestran los siguientes encabezados de respuesta agregados por el código de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="e9ed6-805">**autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="e9ed6-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="e9ed6-807">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="e9ed6-807">**internal:** `My header`</span></span>

<span data-ttu-id="e9ed6-808">El código anterior crea el encabezado de respuesta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="e9ed6-809">IFilterFactory implementado en un atributo</span><span class="sxs-lookup"><span data-stu-id="e9ed6-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="e9ed6-810">Los filtros que implementan `IFilterFactory` son útiles para los filtros que:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="e9ed6-811">No requieren pasar parámetros.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="e9ed6-812">Tienen dependencias de constructor que deben completarse por medio de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="e9ed6-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="e9ed6-814">`IFilterFactory` expone el método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para crear una instancia de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="e9ed6-815">`CreateInstance` carga el tipo especificado desde el contenedor de servicios (inserción de dependencias).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="e9ed6-816">El código siguiente muestra tres métodos para aplicar `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="e9ed6-817">En el código anterior, decorar el método con `[SampleActionFilter]` es el enfoque preferido para aplicar `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="e9ed6-818">Uso de middleware en la canalización de filtro</span><span class="sxs-lookup"><span data-stu-id="e9ed6-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="e9ed6-819">Los filtros de recursos funcionan como el [middleware](xref:fundamentals/middleware/index), en el sentido de que se encargan de la ejecución de todo lo que viene después en la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="e9ed6-820">Pero los filtros se diferencian del middleware en que forman parte del entorno de ejecución de ASP.NET Core, lo que significa que tienen acceso al contexto y las construcciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="e9ed6-821">Para usar middleware como un filtro, cree un tipo con un método `Configure` en el que se especifique el middleware para insertar en la canalización de filtro.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="e9ed6-822">El ejemplo siguiente usa el middleware de localización para establecer la referencia cultural actual de una solicitud:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="e9ed6-823">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para ejecutar el middleware:</span><span class="sxs-lookup"><span data-stu-id="e9ed6-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="e9ed6-824">Los filtros de middleware se ejecutan en la misma fase de la canalización de filtro que los filtros de recursos, antes del enlace de modelos y después del resto de la canalización.</span><span class="sxs-lookup"><span data-stu-id="e9ed6-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="e9ed6-825">Siguientes acciones</span><span class="sxs-lookup"><span data-stu-id="e9ed6-825">Next actions</span></span>

* <span data-ttu-id="e9ed6-826">Vea [métodos de filtro Razor para páginas](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="e9ed6-827">Para experimentar con los filtros, [descargue, pruebe y modifique el ejemplo de GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="e9ed6-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
