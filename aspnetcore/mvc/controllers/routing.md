---
title: Enrutar a acciones de controlador de ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo ASP.NET Core MVC usa el middleware de enrutamiento para encontrar direcciones URL de las solicitudes entrantes y asignarlas a acciones.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 74afd0a076ca8bd753000f547ef0a26308e8a884
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123497"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="21cc5-103">Enrutar a acciones de controlador de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21cc5-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="21cc5-104">Por [Ryan Nowak,](https://github.com/rynowak) [Kirk Larkin](https://twitter.com/serpent5)y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21cc5-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="21cc5-105">ASP.NET Controladores principales usan el [middleware](xref:fundamentals/middleware/index) de enrutamiento para hacer coincidir las direcciones URL de las solicitudes entrantes y asignarlas a [acciones.](#action)</span><span class="sxs-lookup"><span data-stu-id="21cc5-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="21cc5-106">Plantillas de rutas:</span><span class="sxs-lookup"><span data-stu-id="21cc5-106">Routes templates:</span></span>

* <span data-ttu-id="21cc5-107">Se definen en el código de inicio o atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="21cc5-108">Describir cómo las rutas de url coinciden con [las acciones.](#action)</span><span class="sxs-lookup"><span data-stu-id="21cc5-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="21cc5-109">Se utilizan para generar direcciones URL para vínculos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="21cc5-110">Los vínculos generados normalmente se devuelven en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="21cc5-111">Las acciones se [enrutan convencionalmente](#cr) o [se enrutan](#ar)a atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="21cc5-112">Colocar una ruta en el controlador o [la acción](#action) hace que se enrute a tribu.</span><span class="sxs-lookup"><span data-stu-id="21cc5-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="21cc5-113">Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="21cc5-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="21cc5-114">Este documento:</span><span class="sxs-lookup"><span data-stu-id="21cc5-114">This document:</span></span>

* <span data-ttu-id="21cc5-115">Explica las interacciones entre MVC y enrutamiento:</span><span class="sxs-lookup"><span data-stu-id="21cc5-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="21cc5-116">Cómo las aplicaciones MVC típicas hacen uso de las características de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="21cc5-117">Cubre ambos:</span><span class="sxs-lookup"><span data-stu-id="21cc5-117">Covers both:</span></span>
    * <span data-ttu-id="21cc5-118">[El enrutamiento convencional](#cr) se utiliza normalmente con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="21cc5-119">*Enrutamiento de atributos* utilizado con las API de REST.</span><span class="sxs-lookup"><span data-stu-id="21cc5-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="21cc5-120">Si está interesado principalmente en el enrutamiento de las API de REST, vaya a la sección Enrutamiento de atributos [para API de REST.](#ar)</span><span class="sxs-lookup"><span data-stu-id="21cc5-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="21cc5-121">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener detalles avanzados de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="21cc5-122">Hace referencia al sistema de enrutamiento predeterminado agregado en ASP.NET Core 3.0, denominado enrutamiento de punto final.</span><span class="sxs-lookup"><span data-stu-id="21cc5-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="21cc5-123">Es posible utilizar controladores con la versión anterior de enrutamiento con fines de compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="21cc5-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="21cc5-124">Consulte la guía de [migración 2.2-3.0](xref:migration/22-to-30) para obtener instrucciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="21cc5-125">Refiera a la [versión 2.2 de este documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para el material de referencia en el sistema de ruteo heredado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="21cc5-126">Configurar la ruta convencional</span><span class="sxs-lookup"><span data-stu-id="21cc5-126">Set up conventional route</span></span>

<span data-ttu-id="21cc5-127">`Startup.Configure`típicamente tiene código similar al siguiente cuando se utiliza [el enrutamiento convencional:](#crd)</span><span class="sxs-lookup"><span data-stu-id="21cc5-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="21cc5-128">Dentro de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>la <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> llamada a , se utiliza para crear una sola ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="21cc5-129">La ruta única `default` se denomina ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-129">The single route is named `default` route.</span></span> <span data-ttu-id="21cc5-130">La mayoría de las aplicaciones con controladores y vistas usan una plantilla de ruta similar a la `default` ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="21cc5-131">Las API de REST deben usar [el enrutamiento de atributos.](#ar)</span><span class="sxs-lookup"><span data-stu-id="21cc5-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="21cc5-132">La plantilla `"{controller=Home}/{action=Index}/{id?}"`de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="21cc5-133">Coincide con una ruta url como`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="21cc5-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="21cc5-134">Extrae los `{ controller = Products, action = Details, id = 5 }` valores de ruta tokenizando la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="21cc5-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="21cc5-135">La extracción de valores de ruta da como `ProductsController` resultado `Details` una coincidencia si la aplicación tiene un controlador con nombre y una acción:</span><span class="sxs-lookup"><span data-stu-id="21cc5-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="21cc5-136">`/Products/Details/5`modelo enlaza el `id = 5` valor de `id` para `5`establecer el parámetro en .</span><span class="sxs-lookup"><span data-stu-id="21cc5-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="21cc5-137">Consulte [Enlace de modelos](xref:mvc/models/model-binding) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="21cc5-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="21cc5-138">`{controller=Home}`define `Home` como `controller`el valor predeterminado .</span><span class="sxs-lookup"><span data-stu-id="21cc5-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="21cc5-139">`{action=Index}`define `Index` como `action`el valor predeterminado .</span><span class="sxs-lookup"><span data-stu-id="21cc5-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="21cc5-140">El `?` carácter en `{id?}` define `id` como opcional.</span><span class="sxs-lookup"><span data-stu-id="21cc5-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="21cc5-141">No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="21cc5-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="21cc5-142">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="21cc5-143">Coincide con `/`la ruta de acceso url .</span><span class="sxs-lookup"><span data-stu-id="21cc5-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="21cc5-144">Produce los valores `{ controller = Home, action = Index }`de ruta .</span><span class="sxs-lookup"><span data-stu-id="21cc5-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="21cc5-145">Los valores `controller` `action` para y hacer uso de los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="21cc5-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="21cc5-146">`id`no produce un valor ya que no hay ningún segmento correspondiente en la ruta de acceso URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="21cc5-147">`/`sólo coincide si existe `HomeController` `Index` una y acción:</span><span class="sxs-lookup"><span data-stu-id="21cc5-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="21cc5-148">Mediante la definición de controlador `HomeController.Index` anterior y la plantilla de ruta, la acción se ejecuta para las siguientes rutas de URL:</span><span class="sxs-lookup"><span data-stu-id="21cc5-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="21cc5-149">La ruta `/` de acceso `Home` URL `Index` utiliza los controladores y la acción predeterminados de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="21cc5-150">La ruta `/Home` de acceso `Index` URL utiliza la acción predeterminada de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="21cc5-151">El método de conveniencia <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="21cc5-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="21cc5-152">Reemplaza:</span><span class="sxs-lookup"><span data-stu-id="21cc5-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="21cc5-153">El enrutamiento se <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> configura mediante el middleware y.</span><span class="sxs-lookup"><span data-stu-id="21cc5-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="21cc5-154">Para utilizar controladores:</span><span class="sxs-lookup"><span data-stu-id="21cc5-154">To use controllers:</span></span>
>
> * <span data-ttu-id="21cc5-155">Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` dentro para asignar controladores [enrutados de atributos.](#ar)</span><span class="sxs-lookup"><span data-stu-id="21cc5-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="21cc5-156">Llame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>o , para asignar controladores [enrutados convencionalmente.](#cr)</span><span class="sxs-lookup"><span data-stu-id="21cc5-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="21cc5-157">Enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="21cc5-157">Conventional routing</span></span>

<span data-ttu-id="21cc5-158">El enrutamiento convencional se utiliza con controladores y vistas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="21cc5-159">La ruta `default`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="21cc5-160">es un ejemplo de un *enrutamiento convencional*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="21cc5-161">Se llama *enrutamiento convencional* porque establece una *convención* para las rutas de URL:</span><span class="sxs-lookup"><span data-stu-id="21cc5-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="21cc5-162">El primer segmento `{controller=Home}`de ruta de acceso, , se asigna al nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="21cc5-163">El segundo `{action=Index}`segmento, , se asigna al nombre de la [acción.](#action)</span><span class="sxs-lookup"><span data-stu-id="21cc5-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="21cc5-164">El tercer `{id?}` segmento, se `id`utiliza para un archivo .</span><span class="sxs-lookup"><span data-stu-id="21cc5-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="21cc5-165">El `?` `{id?}` in lo hace opcional.</span><span class="sxs-lookup"><span data-stu-id="21cc5-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="21cc5-166">`id`se utiliza para asignar a una entidad de modelo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="21cc5-167">Usando `default` esta ruta, la trayectoria URL:</span><span class="sxs-lookup"><span data-stu-id="21cc5-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="21cc5-168">`/Products/List`mapas a `ProductsController.List` la acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="21cc5-169">`/Blog/Article/17`se `BlogController.Article` asigna a y normalmente el modelo enlaza el `id` parámetro a 17.</span><span class="sxs-lookup"><span data-stu-id="21cc5-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="21cc5-170">Esta asignación:</span><span class="sxs-lookup"><span data-stu-id="21cc5-170">This mapping:</span></span>

* <span data-ttu-id="21cc5-171">Se basa **únicamente**en el controlador y los nombres de [acción.](#action)</span><span class="sxs-lookup"><span data-stu-id="21cc5-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="21cc5-172">No se basa en espacios de nombres, ubicaciones de archivos de origen o parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="21cc5-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="21cc5-173">El uso de enrutamiento convencional con la ruta predeterminada permite crear la aplicación sin tener que crear un nuevo patrón de URL para cada acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="21cc5-174">Para una aplicación con acciones de estilo [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) con coherencia para las direcciones URL en todos los controladores:</span><span class="sxs-lookup"><span data-stu-id="21cc5-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="21cc5-175">Ayuda a simplificar el código.</span><span class="sxs-lookup"><span data-stu-id="21cc5-175">Helps simplify the code.</span></span>
* <span data-ttu-id="21cc5-176">Hace que la interfaz de usuario sea más predecible.</span><span class="sxs-lookup"><span data-stu-id="21cc5-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="21cc5-177">El `id` código anterior se define como opcional por la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="21cc5-178">Las acciones se pueden ejecutar sin el identificador opcional proporcionado como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="21cc5-179">Generalmente,`id` cuando se omite de la dirección URL:</span><span class="sxs-lookup"><span data-stu-id="21cc5-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="21cc5-180">`id`se establece `0` en el enlace de modelos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="21cc5-181">No se encuentra ninguna `id == 0`entidad en la coincidencia de base de datos .</span><span class="sxs-lookup"><span data-stu-id="21cc5-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="21cc5-182">[El enrutamiento](#ar) de atributos proporciona un control detallado para que el identificador sea necesario para algunas acciones y no para otras.</span><span class="sxs-lookup"><span data-stu-id="21cc5-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="21cc5-183">Por convención, la documentación `id` incluye parámetros opcionales como cuándo es probable que aparezcan en el uso correcto.</span><span class="sxs-lookup"><span data-stu-id="21cc5-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="21cc5-184">La mayoría de las aplicaciones deben elegir un esquema de enrutamiento básico y descriptivo para que las direcciones URL sean legibles y significativas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="21cc5-185">La ruta convencional predeterminada `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="21cc5-186">Admite un esquema de enrutamiento básico y descriptivo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="21cc5-187">Se trata de un punto de partida útil para las aplicaciones basadas en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="21cc5-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="21cc5-188">Es la única plantilla de ruta necesaria para muchas aplicaciones de interfaz de usuario web.</span><span class="sxs-lookup"><span data-stu-id="21cc5-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="21cc5-189">Para aplicaciones de interfaz de usuario web más grandes, otra ruta que use [Areas](#areas) si con frecuencia todo lo que se necesita.</span><span class="sxs-lookup"><span data-stu-id="21cc5-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="21cc5-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>y <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="21cc5-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="21cc5-191">Asigne automáticamente un valor de **pedido** a sus puntos finales en función del orden en que se invocan.</span><span class="sxs-lookup"><span data-stu-id="21cc5-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="21cc5-192">Enrutamiento de punto final en ASP.NET Core 3.0 y versiones posteriores:</span><span class="sxs-lookup"><span data-stu-id="21cc5-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="21cc5-193">No tiene un concepto de rutas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="21cc5-194">No proporciona garantías de ordenación para la ejecución de la extensibilidad, todos los puntos de conexión se procesan a la vez.</span><span class="sxs-lookup"><span data-stu-id="21cc5-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="21cc5-195">Habilite el [registro](xref:fundamentals/logging/index) para ver de qué forma las implementaciones de enrutamiento integradas, como <xref:Microsoft.AspNetCore.Routing.Route>, coinciden con las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="21cc5-196">[El ruteo](#ar) de atributos se explica más adelante en este documento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="21cc5-197">Múltiples rutas convencionales</span><span class="sxs-lookup"><span data-stu-id="21cc5-197">Multiple conventional routes</span></span>

<span data-ttu-id="21cc5-198">Múltiples [rutas convencionales](#cr) se `UseEndpoints` pueden agregar <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> en <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>el interior mediante la adición de más llamadas a y .</span><span class="sxs-lookup"><span data-stu-id="21cc5-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="21cc5-199">Esto permite definir varias convenciones, o agregar rutas convencionales dedicadas a una [acción](#action)específica, como:</span><span class="sxs-lookup"><span data-stu-id="21cc5-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="21cc5-200">La `blog` ruta en el código anterior es una **ruta convencional dedicada.**</span><span class="sxs-lookup"><span data-stu-id="21cc5-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="21cc5-201">Se llama una ruta convencional dedicada porque:</span><span class="sxs-lookup"><span data-stu-id="21cc5-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="21cc5-202">Utiliza [enrutamiento convencional.](#cr)</span><span class="sxs-lookup"><span data-stu-id="21cc5-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="21cc5-203">Está dedicado a una [acción](#action)específica.</span><span class="sxs-lookup"><span data-stu-id="21cc5-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="21cc5-204">Porque `controller` `action` y no aparecen en `"blog/{*article}"` la plantilla de ruta como parámetros:</span><span class="sxs-lookup"><span data-stu-id="21cc5-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="21cc5-205">Solo pueden tener los `{ controller = "Blog", action = "Article" }`valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="21cc5-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="21cc5-206">Esta ruta siempre se `BlogController.Article`asigna a la acción .</span><span class="sxs-lookup"><span data-stu-id="21cc5-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="21cc5-207">`/Blog`, `/Blog/Article`, `/Blog/{any-string}` y son las únicas rutas de URL que coinciden con la ruta del blog.</span><span class="sxs-lookup"><span data-stu-id="21cc5-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="21cc5-208">El ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-208">The preceding example:</span></span>

* <span data-ttu-id="21cc5-209">`blog`la ruta tiene una prioridad `default` más alta para las coincidencias que la ruta porque se agrega primero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="21cc5-210">Es y ejemplo de enrutamiento de estilo [Slug](https://developer.mozilla.org/docs/Glossary/Slug) donde es típico tener un nombre de artículo como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="21cc5-211">En ASP.NET Core 3.0 y versiones posteriores, el enrutamiento no:</span><span class="sxs-lookup"><span data-stu-id="21cc5-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="21cc5-212">Defina un concepto denominado *ruta*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-212">Define a concept called a *route*.</span></span> <span data-ttu-id="21cc5-213">`UseRouting`agrega coincidencia de ruta a la canalización de middleware.</span><span class="sxs-lookup"><span data-stu-id="21cc5-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="21cc5-214">El `UseRouting` middleware examina el conjunto de puntos de conexión definidos en la aplicación y selecciona la mejor coincidencia de punto de conexión en función de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="21cc5-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="21cc5-215">Proporcionar garantías sobre el orden de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>ejecución de extensibilidad como o .</span><span class="sxs-lookup"><span data-stu-id="21cc5-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="21cc5-216">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener material de referencia en el enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="21cc5-217">Orden de enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="21cc5-217">Conventional routing order</span></span>

<span data-ttu-id="21cc5-218">El enrutamiento convencional solo coincide con una combinación de acción y controlador definidos por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="21cc5-219">Esto está destinado a simplificar los casos en los que las rutas convencionales se superponen.</span><span class="sxs-lookup"><span data-stu-id="21cc5-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="21cc5-220">Agregar rutas <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>mediante <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , y asignar automáticamente un valor de pedido a sus puntos finales en función del orden en que se invocan.</span><span class="sxs-lookup"><span data-stu-id="21cc5-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="21cc5-221">Las coincidencias de una ruta que aparece anteriormente tienen una prioridad más alta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="21cc5-222">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="21cc5-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="21cc5-223">En general, las rutas con áreas deben colocarse antes, ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="21cc5-224">[Las rutas convencionales dedicadas](#dcr) con `{*article}` captura de todos los parámetros de ruta como puede hacer que una ruta demasiado [codiciosa,](xref:fundamentals/routing#greedy)lo que significa que coincide con las direcciones URL que pretendía que coincidan con otras rutas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="21cc5-225">Coloque las rutas codiciosas más adelante en la tabla de rutas para evitar coincidencias codiciosas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="21cc5-226">Resolver acciones ambiguas</span><span class="sxs-lookup"><span data-stu-id="21cc5-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="21cc5-227">Cuando dos puntos finales coinciden con el enrutamiento, el enrutamiento debe realizar una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="21cc5-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="21cc5-228">Elige el mejor candidato.</span><span class="sxs-lookup"><span data-stu-id="21cc5-228">Choose the best candidate.</span></span>
* <span data-ttu-id="21cc5-229">Iniciar una excepción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-229">Throw an exception.</span></span>

<span data-ttu-id="21cc5-230">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="21cc5-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="21cc5-231">El controlador anterior define dos acciones que coinciden:</span><span class="sxs-lookup"><span data-stu-id="21cc5-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="21cc5-232">La ruta URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="21cc5-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="21cc5-233">Datos `{ controller = Products33, action = Edit, id = 17 }`de ruta .</span><span class="sxs-lookup"><span data-stu-id="21cc5-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="21cc5-234">Este es un patrón típico para los controladores MVC:</span><span class="sxs-lookup"><span data-stu-id="21cc5-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="21cc5-235">`Edit(int)`muestra un formulario para editar un producto.</span><span class="sxs-lookup"><span data-stu-id="21cc5-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="21cc5-236">`Edit(int, Product)`procesa el formulario registrado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="21cc5-237">Para resolver la ruta correcta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-237">To resolve the correct route:</span></span>

* <span data-ttu-id="21cc5-238">`Edit(int, Product)`se selecciona cuando la `POST`solicitud es HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="21cc5-239">`Edit(int)`se selecciona cuando el [verbo HTTP](#verb) es cualquier otra cosa.</span><span class="sxs-lookup"><span data-stu-id="21cc5-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="21cc5-240">`Edit(int)`generalmente se `GET`llama a través de .</span><span class="sxs-lookup"><span data-stu-id="21cc5-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="21cc5-241">El <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, , se proporciona al enrutamiento para que pueda elegir en función del método HTTP de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="21cc5-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="21cc5-242">El `HttpPostAttribute` `Edit(int, Product)` hace una `Edit(int)`mejor coincidencia que .</span><span class="sxs-lookup"><span data-stu-id="21cc5-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="21cc5-243">Es importante entender el papel de `HttpPostAttribute`atributos como .</span><span class="sxs-lookup"><span data-stu-id="21cc5-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="21cc5-244">Se definen atributos similares para otros [verbos HTTP.](#verb)</span><span class="sxs-lookup"><span data-stu-id="21cc5-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="21cc5-245">En el [enrutamiento convencional,](#cr)es común que las acciones usen el mismo nombre de acción cuando forman parte de un formulario de inicio, envían el flujo de trabajo del formulario.</span><span class="sxs-lookup"><span data-stu-id="21cc5-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="21cc5-246">Por ejemplo, vea [Examinar los dos métodos](xref:tutorials/first-mvc-app/controller-methods-views#get-post)de acción Editar .</span><span class="sxs-lookup"><span data-stu-id="21cc5-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="21cc5-247">Si el enrutamiento no puede elegir <xref:System.Reflection.AmbiguousMatchException> un mejor candidato, se lanza un, que enumera los varios puntos de conexión coincidentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="21cc5-248">Nombres de rutas convencionales</span><span class="sxs-lookup"><span data-stu-id="21cc5-248">Conventional route names</span></span>

<span data-ttu-id="21cc5-249">Las `"blog"` cadenas `"default"` y en los siguientes ejemplos son nombres de ruta convencionales:</span><span class="sxs-lookup"><span data-stu-id="21cc5-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="21cc5-250">Los nombres de ruta dan un nombre lógico a la ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-250">The route names give the route a logical name.</span></span> <span data-ttu-id="21cc5-251">La ruta con nombre se puede utilizar para la generación de URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="21cc5-252">El uso de una ruta con nombre simplifica la creación de direcciones URL cuando el orden de las rutas podría complicar la generación de URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="21cc5-253">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="21cc5-254">Nombres de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-254">Route names:</span></span>

* <span data-ttu-id="21cc5-255">No tiene ningún impacto en la coincidencia de URL o el control de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="21cc5-256">Solo se utilizan para la generación de URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-256">Are used only for URL generation.</span></span>

<span data-ttu-id="21cc5-257">El concepto de nombre de ruta se representa en el enrutamiento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="21cc5-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="21cc5-258">Los términos nombre de **ruta** y **nombre del punto de conexión:**</span><span class="sxs-lookup"><span data-stu-id="21cc5-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="21cc5-259">Son intercambiables.</span><span class="sxs-lookup"><span data-stu-id="21cc5-259">Are interchangeable.</span></span>
* <span data-ttu-id="21cc5-260">El que se utiliza en la documentación y el código depende de la API que se describe.</span><span class="sxs-lookup"><span data-stu-id="21cc5-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="21cc5-261">Enrutamiento de atributos para API REST</span><span class="sxs-lookup"><span data-stu-id="21cc5-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="21cc5-262">Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos donde las operaciones se representan mediante [verbos HTTP.](#verb)</span><span class="sxs-lookup"><span data-stu-id="21cc5-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="21cc5-263">El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="21cc5-264">El `StartUp.Configure` código siguiente es típico de una API de REST y se usa en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="21cc5-265">En el código <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> anterior, `UseEndpoints` se llama dentro para asignar controladores enrutados de atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="21cc5-266">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-266">In the following example:</span></span>

* <span data-ttu-id="21cc5-267">Se utiliza `Configure` el método anterior.</span><span class="sxs-lookup"><span data-stu-id="21cc5-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="21cc5-268">`HomeController`coincide con un conjunto de direcciones URL `{controller=Home}/{action=Index}/{id?}` similares a lo que coincide la ruta convencional predeterminada.</span><span class="sxs-lookup"><span data-stu-id="21cc5-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="21cc5-269">La `HomeController.Index` acción se ejecuta para `/`cualquiera `/Home` `/Home/Index`de `/Home/Index/3`las rutas de url , , , o .</span><span class="sxs-lookup"><span data-stu-id="21cc5-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="21cc5-270">En este ejemplo se destaca una diferencia de programación clave entre el enrutamiento de atributos y el [enrutamiento convencional.](#cr)</span><span class="sxs-lookup"><span data-stu-id="21cc5-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="21cc5-271">El enrutamiento de atributos requiere más entrada para especificar una ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="21cc5-272">La ruta predeterminada convencional maneja las rutas de forma más sucinta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="21cc5-273">Sin embargo, el enrutamiento de atributos permite y requiere un control preciso de qué plantillas de ruta se aplican a cada [acción.](#action)</span><span class="sxs-lookup"><span data-stu-id="21cc5-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="21cc5-274">Con el enrutamiento de atributos, el nombre del controlador y los nombres de acción no desempeñan **ningún** papel en el que se coincida con la acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="21cc5-275">El ejemplo siguiente coincide con las mismas direcciones URL que en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="21cc5-276">El código siguiente utiliza `action` `controller`el reemplazo de tokens para y :</span><span class="sxs-lookup"><span data-stu-id="21cc5-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="21cc5-277">El código `[Route("[controller]/[action]")]` siguiente se aplica al controlador:</span><span class="sxs-lookup"><span data-stu-id="21cc5-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="21cc5-278">En el código anterior, las `Index` plantillas `/` `~/` de método deben anteponer o a las plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="21cc5-279">Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="21cc5-280">Consulte [Prioridad de plantilla](xref:fundamentals/routing#rtp) de ruta para obtener información sobre la selección de plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="21cc5-281">Nombres de enrutamientos reservados</span><span class="sxs-lookup"><span data-stu-id="21cc5-281">Reserved routing names</span></span>

<span data-ttu-id="21cc5-282">Las siguientes palabras clave son nombres de parámetros de ruta reservados cuando se utilizan controladores o páginas de razor:</span><span class="sxs-lookup"><span data-stu-id="21cc5-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="21cc5-283">El `page` uso como parámetro de ruta con enrutamiento de atributos es un error común.</span><span class="sxs-lookup"><span data-stu-id="21cc5-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="21cc5-284">Esto da como resultado un comportamiento incoherente y confuso con la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="21cc5-285">La generación de direcciones URL utiliza los nombres de parámetro especiales para determinar si una operación de generación de URL hace referencia a una página de Razor o a un controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="21cc5-286">Plantillas de verbos HTTP</span><span class="sxs-lookup"><span data-stu-id="21cc5-286">HTTP verb templates</span></span>

<span data-ttu-id="21cc5-287">ASP.NET Core tiene las siguientes plantillas de verbos HTTP:</span><span class="sxs-lookup"><span data-stu-id="21cc5-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="21cc5-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="21cc5-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="21cc5-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="21cc5-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="21cc5-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="21cc5-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="21cc5-294">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-294">Route templates</span></span>

<span data-ttu-id="21cc5-295">ASP.NET Core tiene las siguientes plantillas de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="21cc5-296">Todas las plantillas de [verbos HTTP](#verb) son plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="21cc5-297">[[Ruta]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="21cc5-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="21cc5-298">Enrutamiento de atributos con atributos de verbo Http</span><span class="sxs-lookup"><span data-stu-id="21cc5-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="21cc5-299">Considere el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="21cc5-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="21cc5-300">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-300">In the preceding code:</span></span>

* <span data-ttu-id="21cc5-301">Cada acción `[HttpGet]` contiene el atributo, que restringe la coincidencia solo con las solicitudes HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="21cc5-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="21cc5-302">La `GetProduct` acción `"{id}"` incluye la `id` plantilla, por `"api/[controller]"` lo tanto, se anexa a la plantilla en el controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="21cc5-303">La plantilla `"api/[controller]/"{id}""`de métodos es .</span><span class="sxs-lookup"><span data-stu-id="21cc5-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="21cc5-304">Por lo tanto, esta acción solo `/api/test2/xyz``/api/test2/123`coincide`/api/test2/{any string}`con las solicitudes GET del formulario , , , etc.</span><span class="sxs-lookup"><span data-stu-id="21cc5-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="21cc5-305">La `GetIntProduct` acción `"int/{id:int}")` contiene la plantilla.</span><span class="sxs-lookup"><span data-stu-id="21cc5-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="21cc5-306">La `:int` parte de la `id` plantilla restringe los valores de ruta a cadenas que se pueden convertir en un entero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="21cc5-307">Una solicitud `/api/test2/int/abc`GET a:</span><span class="sxs-lookup"><span data-stu-id="21cc5-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="21cc5-308">No coincide con esta acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="21cc5-309">Devuelve un error [404 No encontrado.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)</span><span class="sxs-lookup"><span data-stu-id="21cc5-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="21cc5-310">La `GetInt2Product` acción `{id}` contiene en la plantilla, `id` pero no se restringe a los valores que se pueden convertir en un entero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="21cc5-311">Una solicitud `/api/test2/int2/abc`GET a:</span><span class="sxs-lookup"><span data-stu-id="21cc5-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="21cc5-312">Coincide con esta ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-312">Matches this route.</span></span>
  * <span data-ttu-id="21cc5-313">El enlace de `abc` modelo no puede convertirse en un entero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="21cc5-314">El `id` parámetro del método es entero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="21cc5-315">Devuelve una [solicitud incorrecta 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque`abc` el enlace de modelo no se pudo convertir en un entero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="21cc5-316">El enrutamiento <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> de atributos <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>puede <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>utilizar atributos como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, , y .</span><span class="sxs-lookup"><span data-stu-id="21cc5-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="21cc5-317">Todos los atributos del [verbo HTTP](#verb) aceptan una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="21cc5-318">En el ejemplo siguiente se muestran dos acciones que coinciden con la misma plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="21cc5-319">Uso de `/products3`la ruta URL:</span><span class="sxs-lookup"><span data-stu-id="21cc5-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="21cc5-320">La `MyProductsController.ListProducts` acción se ejecuta `GET`cuando el verbo [HTTP](#verb) es .</span><span class="sxs-lookup"><span data-stu-id="21cc5-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="21cc5-321">La `MyProductsController.CreateProduct` acción se ejecuta `POST`cuando el verbo [HTTP](#verb) es .</span><span class="sxs-lookup"><span data-stu-id="21cc5-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="21cc5-322">Al crear una API de REST, es raro `[Route(...)]` que necesite usar en un método de acción porque la acción acepta todos los métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="21cc5-323">Es mejor usar el atributo de [verbo HTTP](#verb) más específico para ser preciso sin lo que admite la API.</span><span class="sxs-lookup"><span data-stu-id="21cc5-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="21cc5-324">Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="21cc5-325">Las API de REST deben usar el enrutamiento de atributos para modelar la funcionalidad de la aplicación como un conjunto de recursos donde las operaciones se representan mediante verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="21cc5-326">Esto significa que muchas operaciones, por ejemplo, GET y POST en el mismo recurso lógico utilizan la misma dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="21cc5-327">El enrutamiento mediante atributos proporciona un nivel de control que es necesario para diseñar cuidadosamente un diseño de puntos de conexión públicos de la API.</span><span class="sxs-lookup"><span data-stu-id="21cc5-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="21cc5-328">Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="21cc5-329">En el ejemplo `id` siguiente, es necesario como parte de la ruta de acceso de url:</span><span class="sxs-lookup"><span data-stu-id="21cc5-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="21cc5-330">La `Products2ApiController.GetProduct(int)` acción:</span><span class="sxs-lookup"><span data-stu-id="21cc5-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="21cc5-331">Se ejecuta con la ruta de URL como`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="21cc5-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="21cc5-332">No se ejecuta con `/products2`la ruta de acceso url .</span><span class="sxs-lookup"><span data-stu-id="21cc5-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="21cc5-333">El atributo [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que una acción limite los tipos de contenido de la solicitud compatibles.</span><span class="sxs-lookup"><span data-stu-id="21cc5-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="21cc5-334">Para obtener más información, vea Definir tipos de contenido de [solicitud admitidos con el atributo Consumes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="21cc5-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="21cc5-335">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="21cc5-336">Para obtener `[ApiController]`más información sobre , vea [Atributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="21cc5-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="21cc5-337">Nombre de ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-337">Route name</span></span>

<span data-ttu-id="21cc5-338">El código siguiente define un nombre de ruta de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="21cc5-339">Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica.</span><span class="sxs-lookup"><span data-stu-id="21cc5-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="21cc5-340">Nombres de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-340">Route names:</span></span>

* <span data-ttu-id="21cc5-341">No tenga ningún impacto en el comportamiento de coincidencia de URL del enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="21cc5-342">Solo se utilizan para la generación de URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-342">Are only used for URL generation.</span></span>

<span data-ttu-id="21cc5-343">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="21cc5-344">Contraste el código anterior con la ruta `id` predeterminada convencional,`{id?}`que define el parámetro como opcional ( ).</span><span class="sxs-lookup"><span data-stu-id="21cc5-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="21cc5-345">La capacidad de especificar con precisión las `/products` API `/products/5` tiene ventajas, como permitir y enviar a diferentes acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="21cc5-346">Combinación de rutas de atributos</span><span class="sxs-lookup"><span data-stu-id="21cc5-346">Combining attribute routes</span></span>

<span data-ttu-id="21cc5-347">Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="21cc5-348">Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="21cc5-349">La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="21cc5-350">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-350">In the preceding example:</span></span>

* <span data-ttu-id="21cc5-351">La ruta `/products` URL puede coincidir`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="21cc5-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="21cc5-352">La ruta `/products/5` de `ProductsApi.GetProduct(int)`acceso URL puede coincidir con .</span><span class="sxs-lookup"><span data-stu-id="21cc5-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="21cc5-353">Ambas acciones solo coinciden `GET` con HTTP porque `[HttpGet]` están marcadas con el atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="21cc5-354">Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="21cc5-355">En el ejemplo siguiente se hace coincidir un conjunto de rutas de URL similares a la ruta predeterminada.</span><span class="sxs-lookup"><span data-stu-id="21cc5-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="21cc5-356">En la tabla `[Route]` siguiente se explican los atributos del código anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="21cc5-357">Atributo</span><span class="sxs-lookup"><span data-stu-id="21cc5-357">Attribute</span></span>               | <span data-ttu-id="21cc5-358">Combina con`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="21cc5-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="21cc5-359">Define la plantilla de ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="21cc5-360">Sí</span><span class="sxs-lookup"><span data-stu-id="21cc5-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="21cc5-361">Sí</span><span class="sxs-lookup"><span data-stu-id="21cc5-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="21cc5-362">**No**</span><span class="sxs-lookup"><span data-stu-id="21cc5-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="21cc5-363">Sí</span><span class="sxs-lookup"><span data-stu-id="21cc5-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="21cc5-364">Orden de ruta de atributos</span><span class="sxs-lookup"><span data-stu-id="21cc5-364">Attribute route order</span></span>

<span data-ttu-id="21cc5-365">El enrutamiento crea un árbol y coincide con todos los puntos finales simultáneamente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="21cc5-366">Las entradas de ruta se comportan como si estuvieran colocadas en un pedido ideal.</span><span class="sxs-lookup"><span data-stu-id="21cc5-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="21cc5-367">Las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="21cc5-368">Por ejemplo, una `blog/search/{topic}` ruta de atributo como `blog/{*article}`es más específica que una ruta de atributo como .</span><span class="sxs-lookup"><span data-stu-id="21cc5-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="21cc5-369">La `blog/search/{topic}` ruta tiene mayor prioridad, de forma predeterminada, porque es más específica.</span><span class="sxs-lookup"><span data-stu-id="21cc5-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="21cc5-370">Mediante el [enrutamiento convencional,](#cr)el desarrollador es responsable de colocar las rutas en el orden deseado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="21cc5-371">Las rutas de atributos <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> pueden configurar un pedido mediante la propiedad.</span><span class="sxs-lookup"><span data-stu-id="21cc5-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="21cc5-372">Todos los [atributos](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) de `Order` ruta proporcionados por el marco de trabajo incluyen .</span><span class="sxs-lookup"><span data-stu-id="21cc5-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="21cc5-373">Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="21cc5-374">El orden predeterminado es `0`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-374">The default order is `0`.</span></span> <span data-ttu-id="21cc5-375">Establecer una `Order = -1` ruta mediante corridas antes de las rutas que no establecen un pedido.</span><span class="sxs-lookup"><span data-stu-id="21cc5-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="21cc5-376">Establecer una `Order = 1` ruta mediante ejecuciones después del orden de ruta predeterminado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="21cc5-377">**Evite** dependiendo `Order`de .</span><span class="sxs-lookup"><span data-stu-id="21cc5-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="21cc5-378">Si el espacio URL de una aplicación requiere valores de orden explícitos para enrutar correctamente, es probable que también sea confuso para los clientes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="21cc5-379">En general, el enrutamiento de atributos selecciona la ruta correcta con coincidencia de URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="21cc5-380">Si el orden predeterminado utilizado para la generación de direcciones URL no funciona, `Order` usar un nombre de ruta como invalidación suele ser más sencillo que aplicar la propiedad.</span><span class="sxs-lookup"><span data-stu-id="21cc5-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="21cc5-381">Considere los dos controladores siguientes `/home`que definen la coincidencia de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="21cc5-382">Al `/home` solicitar con el código anterior se produce una excepción similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="21cc5-383">Agregar `Order` a uno de los atributos de ruta resuelve la ambiguedad:</span><span class="sxs-lookup"><span data-stu-id="21cc5-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="21cc5-384">Con el código `/home` anterior, `HomeController.Index` ejecuta el punto de conexión.</span><span class="sxs-lookup"><span data-stu-id="21cc5-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="21cc5-385">Para llegar `MyDemoController.MyIndex`a `/home/MyIndex`la solicitud , .</span><span class="sxs-lookup"><span data-stu-id="21cc5-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="21cc5-386">**Nota**:</span><span class="sxs-lookup"><span data-stu-id="21cc5-386">**Note**:</span></span>

* <span data-ttu-id="21cc5-387">El código anterior es un ejemplo o un diseño de enrutamiento deficiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="21cc5-388">Se utilizó para `Order` ilustrar la propiedad.</span><span class="sxs-lookup"><span data-stu-id="21cc5-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="21cc5-389">La `Order` propiedad solo resuelve la ambiguedad, esa plantilla no se puede hacer coincidir.</span><span class="sxs-lookup"><span data-stu-id="21cc5-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="21cc5-390">Sería mejor eliminar la `[Route("Home")]` plantilla.</span><span class="sxs-lookup"><span data-stu-id="21cc5-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="21cc5-391">Consulte Rutas de [Razor Pages y convenciones](xref:razor-pages/razor-pages-conventions#route-order) de aplicaciones: Orden de ruta para obtener información sobre el orden de las rutas con Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="21cc5-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="21cc5-392">En algunos casos, se devuelve un error HTTP 500 con rutas ambiguas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="21cc5-393">Utilice el [registro](xref:fundamentals/logging/index) para ver `AmbiguousMatchException`qué puntos de conexión causaron el archivo .</span><span class="sxs-lookup"><span data-stu-id="21cc5-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="21cc5-394">Reemplazo de tokens en plantillas de ruta [controlador], [acción], [área]</span><span class="sxs-lookup"><span data-stu-id="21cc5-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="21cc5-395">Para mayor comodidad, las rutas de atributos admiten el reemplazo de tokens para parámetros de ruta reservados mediante la encomienda un token en uno de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="21cc5-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="21cc5-396">Llaves cuadradas:`[]`</span><span class="sxs-lookup"><span data-stu-id="21cc5-396">Square braces: `[]`</span></span>
* <span data-ttu-id="21cc5-397">Llaves rizadas:`{}`</span><span class="sxs-lookup"><span data-stu-id="21cc5-397">Curly braces: `{}`</span></span>

<span data-ttu-id="21cc5-398">Los `[action]`tokens `[area]`, `[controller]` , y se reemplazan por los valores del nombre de la acción, el nombre del área y el nombre del controlador de la acción donde se define la ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="21cc5-399">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="21cc5-400">Partidos`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="21cc5-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="21cc5-401">Partidos`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="21cc5-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="21cc5-402">El reemplazo del token se produce como último paso de la creación de las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="21cc5-403">El ejemplo anterior se comporta igual que el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="21cc5-404">Las rutas de atributo también se pueden combinar con la herencia.</span><span class="sxs-lookup"><span data-stu-id="21cc5-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="21cc5-405">Esto es potente combinado con el reemplazo de tokens.</span><span class="sxs-lookup"><span data-stu-id="21cc5-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="21cc5-406">El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="21cc5-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nombre de ruta único para cada acción:</span><span class="sxs-lookup"><span data-stu-id="21cc5-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="21cc5-408">El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="21cc5-409"> genera un nombre de ruta único para cada acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="21cc5-410">Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.</span><span class="sxs-lookup"><span data-stu-id="21cc5-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="21cc5-411">Usar un transformador de parámetro para personalizar el reemplazo de tokens</span><span class="sxs-lookup"><span data-stu-id="21cc5-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="21cc5-412">El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro.</span><span class="sxs-lookup"><span data-stu-id="21cc5-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="21cc5-413">Un transformador de parámetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> y transforma el valor de parámetros.</span><span class="sxs-lookup"><span data-stu-id="21cc5-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="21cc5-414">Por ejemplo, `SlugifyParameterTransformer` un transformador `SubscriptionManagement` de parámetros personalizado cambia el valor de ruta a: `subscription-management`</span><span class="sxs-lookup"><span data-stu-id="21cc5-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="21cc5-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> es una convención de modelo de aplicación que:</span><span class="sxs-lookup"><span data-stu-id="21cc5-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="21cc5-416">Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="21cc5-417">Personaliza los valores de token de ruta de atributo que se reemplazan.</span><span class="sxs-lookup"><span data-stu-id="21cc5-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="21cc5-418">El `ListAll` método anterior `/subscription-management/list-all`coincide con .</span><span class="sxs-lookup"><span data-stu-id="21cc5-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="21cc5-419">`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="21cc5-420">Consulte documentos web de [MDN en Slug](https://developer.mozilla.org/docs/Glossary/Slug) para obtener la definición de Slug.</span><span class="sxs-lookup"><span data-stu-id="21cc5-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="21cc5-421">Múltiples rutas de atributos</span><span class="sxs-lookup"><span data-stu-id="21cc5-421">Multiple attribute routes</span></span>

<span data-ttu-id="21cc5-422">El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="21cc5-423">El uso más común de esto es imitar el comportamiento de la ruta convencional predeterminada tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="21cc5-424">Poner varios atributos de ruta en el controlador significa que cada uno se combina con cada uno de los atributos de ruta en los métodos de acción:</span><span class="sxs-lookup"><span data-stu-id="21cc5-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="21cc5-425">Todas las restricciones de `IActionConstraint`ruta de verbo [HTTP](#verb) implementan .</span><span class="sxs-lookup"><span data-stu-id="21cc5-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="21cc5-426">Cuando se colocan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> varios atributos de ruta que se implementan en una acción:</span><span class="sxs-lookup"><span data-stu-id="21cc5-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="21cc5-427">Cada restricción de acción se combina con la plantilla de ruta aplicada al controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="21cc5-428">El uso de varias rutas en acciones puede parecer útil y eficaz, es mejor mantener el espacio de URL de la aplicación básico y bien definido.</span><span class="sxs-lookup"><span data-stu-id="21cc5-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="21cc5-429">Utilice varias rutas en acciones **solo** cuando sea necesario, por ejemplo, para admitir clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="21cc5-430">Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones</span><span class="sxs-lookup"><span data-stu-id="21cc5-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="21cc5-431">Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="21cc5-432">En el código `[HttpPost("product/{id:int}")]` anterior, aplica una restricción de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="21cc5-433">La `ProductsController.ShowProduct` acción solo se compara `/product/3`con rutas de URL como .</span><span class="sxs-lookup"><span data-stu-id="21cc5-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="21cc5-434">La parte `{id:int}` de la plantilla de ruta restringe ese segmento solo a enteros.</span><span class="sxs-lookup"><span data-stu-id="21cc5-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="21cc5-435">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="21cc5-436">Atributos de ruta personalizados mediante IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="21cc5-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="21cc5-437">Todos los [atributos](#rt) de ruta implementan <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="21cc5-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="21cc5-438">El tiempo de ejecución de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="21cc5-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="21cc5-439">Busca atributos en las clases de controlador y los métodos de acción cuando se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="21cc5-440">Utiliza los atributos que se implementan `IRouteTemplateProvider` para crear el conjunto inicial de rutas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="21cc5-441">Implementar `IRouteTemplateProvider` para definir atributos de ruta personalizados.</span><span class="sxs-lookup"><span data-stu-id="21cc5-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="21cc5-442">Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:</span><span class="sxs-lookup"><span data-stu-id="21cc5-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="21cc5-443">El `Get` método anterior `Order = 2, Template = api/MyTestApi`devuelve .</span><span class="sxs-lookup"><span data-stu-id="21cc5-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="21cc5-444">Utilice el modelo de aplicación para personalizar las rutas de atributos</span><span class="sxs-lookup"><span data-stu-id="21cc5-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="21cc5-445">El modelo de aplicación:</span><span class="sxs-lookup"><span data-stu-id="21cc5-445">The application model:</span></span>

* <span data-ttu-id="21cc5-446">Es un modelo de objetos creado al inicio.</span><span class="sxs-lookup"><span data-stu-id="21cc5-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="21cc5-447">Contiene todos los metadatos utilizados por ASP.NET Core para enrutar y ejecutar las acciones en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="21cc5-448">El modelo de aplicación incluye todos los datos recopilados de los atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="21cc5-449">La `IRouteTemplateProvider` implementación proporciona los datos de los atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="21cc5-450">Convenciones:</span><span class="sxs-lookup"><span data-stu-id="21cc5-450">Conventions:</span></span>

* <span data-ttu-id="21cc5-451">Se puede escribir para modificar el modelo de aplicación para personalizar el comportamiento del enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="21cc5-452">Se leen al inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-452">Are read at app startup.</span></span>

<span data-ttu-id="21cc5-453">En esta sección se muestra un ejemplo básico de personalización del enrutamiento mediante el modelo de aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="21cc5-454">El código siguiente hace que las rutas se alineen aproximadamente con la estructura de carpetas del proyecto.</span><span class="sxs-lookup"><span data-stu-id="21cc5-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="21cc5-455">El código siguiente `namespace` impide que la convención se aplique a los controladores que se enrutan por atributos:</span><span class="sxs-lookup"><span data-stu-id="21cc5-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="21cc5-456">Por ejemplo, el siguiente controlador `NamespaceRoutingConvention`no utiliza:</span><span class="sxs-lookup"><span data-stu-id="21cc5-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="21cc5-457">El método `NamespaceRoutingConvention.Apply` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="21cc5-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="21cc5-458">No hace nada si el controlador es atributo enrutado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="21cc5-459">Establece la plantilla de `namespace`controladores `namespace` en función de la , con la base eliminada.</span><span class="sxs-lookup"><span data-stu-id="21cc5-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="21cc5-460">El `NamespaceRoutingConvention` se puede `Startup.ConfigureServices`aplicar en:</span><span class="sxs-lookup"><span data-stu-id="21cc5-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="21cc5-461">Por ejemplo, considere el siguiente controlador:</span><span class="sxs-lookup"><span data-stu-id="21cc5-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="21cc5-462">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="21cc5-462">In the preceding code:</span></span>

* <span data-ttu-id="21cc5-463">La `namespace` base `My.Application`es .</span><span class="sxs-lookup"><span data-stu-id="21cc5-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="21cc5-464">El nombre completo del controlador `My.Application.Admin.Controllers.UsersController`anterior es .</span><span class="sxs-lookup"><span data-stu-id="21cc5-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="21cc5-465">El `NamespaceRoutingConvention` establece la `Admin/Controllers/Users/[action]/{id?`plantilla de controladores en .</span><span class="sxs-lookup"><span data-stu-id="21cc5-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="21cc5-466">El `NamespaceRoutingConvention` también se puede aplicar como un atributo en un regulador:</span><span class="sxs-lookup"><span data-stu-id="21cc5-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="21cc5-467">Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="21cc5-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="21cc5-468">ASP.NET aplicaciones principales pueden mezclar el uso de enrutamiento convencional y enrutamiento de atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="21cc5-469">Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.</span><span class="sxs-lookup"><span data-stu-id="21cc5-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="21cc5-470">Las acciones se enrutan bien mediante convención o bien mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="21cc5-471">Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="21cc5-472">Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa.</span><span class="sxs-lookup"><span data-stu-id="21cc5-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="21cc5-473">**Cualquier** atributo de ruta en el controlador realiza **todas las** acciones en el atributo de controlador enrutado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="21cc5-474">El enrutamiento de atributos y el enrutamiento convencional utilizan el mismo motor de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="21cc5-475">Generación de URL y valores ambientales</span><span class="sxs-lookup"><span data-stu-id="21cc5-475">URL Generation and ambient values</span></span>

<span data-ttu-id="21cc5-476">Las aplicaciones pueden usar características de generación de URL de enrutamiento para generar vínculos URL a acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="21cc5-477">La generación de direcciones URL elimina las direcciones URL de codificación rígida, lo que hace que el código sea más robusto y mantenible.</span><span class="sxs-lookup"><span data-stu-id="21cc5-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="21cc5-478">Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo cubren los conceptos básicos de cómo funciona la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="21cc5-479">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="21cc5-480">La <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interfaz es el elemento subyacente de la infraestructura entre MVC y enrutamiento para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="21cc5-481">Una instancia `IUrlHelper` de está `Url` disponible a través de la propiedad en controladores, vistas y componentes de vista.</span><span class="sxs-lookup"><span data-stu-id="21cc5-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="21cc5-482">En el ejemplo `IUrlHelper` siguiente, la `Controller.Url` interfaz se utiliza a través de la propiedad para generar una dirección URL a otra acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="21cc5-483">Si la aplicación utiliza la ruta convencional `url` predeterminada, el valor `/UrlGeneration/Destination`de la variable es la cadena de ruta de acceso URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="21cc5-484">Esta ruta de acceso URL se crea mediante el enrutamiento combinando:</span><span class="sxs-lookup"><span data-stu-id="21cc5-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="21cc5-485">Los valores de ruta de la solicitud actual, que se denominan **valores ambiente.**</span><span class="sxs-lookup"><span data-stu-id="21cc5-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="21cc5-486">Los valores `Url.Action` pasados y sustituyendo esos valores en la plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="21cc5-487">El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="21cc5-488">Un parámetro de ruta que no tiene un valor puede:</span><span class="sxs-lookup"><span data-stu-id="21cc5-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="21cc5-489">Utilice un valor predeterminado si tiene uno.</span><span class="sxs-lookup"><span data-stu-id="21cc5-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="21cc5-490">Se omitirá si es opcional.</span><span class="sxs-lookup"><span data-stu-id="21cc5-490">Be skipped if it's optional.</span></span> <span data-ttu-id="21cc5-491">Por ejemplo, `id` la plantilla `{controller}/{action}/{id?}`de ruta .</span><span class="sxs-lookup"><span data-stu-id="21cc5-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="21cc5-492">Se produce un error en la generación de URL si cualquier parámetro de ruta necesario no tiene un valor correspondiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="21cc5-493">Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="21cc5-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="21cc5-494">El ejemplo anterior `Url.Action` de assumes [conventional routing](#cr).</span><span class="sxs-lookup"><span data-stu-id="21cc5-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="21cc5-495">La generación de URL funciona de forma similar con el enrutamiento de [atributos,](#ar)aunque los conceptos son diferentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="21cc5-496">Con enrutamiento convencional:</span><span class="sxs-lookup"><span data-stu-id="21cc5-496">With conventional routing:</span></span>

* <span data-ttu-id="21cc5-497">Los valores de ruta se utilizan para expandir una plantilla.</span><span class="sxs-lookup"><span data-stu-id="21cc5-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="21cc5-498">Los valores `controller` de `action` ruta para y su ella suelen aparecer en esa plantilla.</span><span class="sxs-lookup"><span data-stu-id="21cc5-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="21cc5-499">Esto funciona porque las direcciones URL coincidentes con el enrutamiento se adhieren a una convención.</span><span class="sxs-lookup"><span data-stu-id="21cc5-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="21cc5-500">En el ejemplo siguiente se utiliza el enrutamiento de atributos:</span><span class="sxs-lookup"><span data-stu-id="21cc5-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="21cc5-501">La `Source` acción del código `custom/url/to/destination`anterior genera .</span><span class="sxs-lookup"><span data-stu-id="21cc5-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="21cc5-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>se añadió en ASP.NET Core 3.0 como alternativa a `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="21cc5-503">`LinkGenerator`ofrece una funcionalidad similar pero más flexible.</span><span class="sxs-lookup"><span data-stu-id="21cc5-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="21cc5-504">Cada método `IUrlHelper` en tiene una `LinkGenerator` familia correspondiente de métodos en, así.</span><span class="sxs-lookup"><span data-stu-id="21cc5-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="21cc5-505">Generación de direcciones URL por nombre de acción</span><span class="sxs-lookup"><span data-stu-id="21cc5-505">Generating URLs by action name</span></span>

<span data-ttu-id="21cc5-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)y todas las sobrecargas relacionadas están diseñadas para generar el punto de conexión de destino especificando un nombre de controlador y un nombre de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="21cc5-507">Cuando `Url.Action`se utiliza , `controller` los `action` valores de ruta actuales para y son proporcionados por el tiempo de ejecución:</span><span class="sxs-lookup"><span data-stu-id="21cc5-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="21cc5-508">El valor `controller` `action` de y son parte de los valores y valores [ambientales.](#ambient)</span><span class="sxs-lookup"><span data-stu-id="21cc5-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="21cc5-509">El `Url.Action` método siempre utiliza `action` los `controller` valores actuales de y genera una ruta de acceso URL que enruta a la acción actual.</span><span class="sxs-lookup"><span data-stu-id="21cc5-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="21cc5-510">El enrutamiento intenta usar los valores de los valores ambientales para rellenar la información que no se proporcionó al generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="21cc5-511">Considere una `{a}/{b}/{c}/{d}` ruta como `{ a = Alice, b = Bob, c = Carol, d = David }`con los valores ambientales:</span><span class="sxs-lookup"><span data-stu-id="21cc5-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="21cc5-512">El enrutamiento tiene suficiente información para generar una dirección URL sin valores adicionales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="21cc5-513">El enrutamiento tiene suficiente información porque todos los parámetros de ruta tienen un valor.</span><span class="sxs-lookup"><span data-stu-id="21cc5-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="21cc5-514">Si se `{ d = Donovan }` añade el valor:</span><span class="sxs-lookup"><span data-stu-id="21cc5-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="21cc5-515">El `{ d = David }` valor se omite.</span><span class="sxs-lookup"><span data-stu-id="21cc5-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="21cc5-516">La ruta de `Alice/Bob/Carol/Donovan`URL generada es .</span><span class="sxs-lookup"><span data-stu-id="21cc5-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="21cc5-517">**Advertencia:** las rutas de URL son jerárquicas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="21cc5-518">En el ejemplo anterior, `{ c = Cheryl }` si se agrega el valor:</span><span class="sxs-lookup"><span data-stu-id="21cc5-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="21cc5-519">Se omiten `{ c = Carol, d = David }` ambos valores.</span><span class="sxs-lookup"><span data-stu-id="21cc5-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="21cc5-520">Ya no hay `d` un valor para y se produce un error en la generación de url.</span><span class="sxs-lookup"><span data-stu-id="21cc5-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="21cc5-521">Los valores `c` deseados de y `d` deben especificarse para generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="21cc5-522">Usted puede ser que espere golpear `{controller}/{action}/{id?}`este problema con la ruta predeterminada.</span><span class="sxs-lookup"><span data-stu-id="21cc5-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="21cc5-523">Este problema es raro `Url.Action` en la práctica `controller` `action` porque siempre especifica explícitamente a y valor.</span><span class="sxs-lookup"><span data-stu-id="21cc5-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="21cc5-524">Varias sobrecargas de [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) toman un objeto de valores `controller` `action`de ruta para proporcionar valores para parámetros de ruta distintos de y .</span><span class="sxs-lookup"><span data-stu-id="21cc5-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="21cc5-525">El objeto de valores de `id`ruta se utiliza con frecuencia con .</span><span class="sxs-lookup"><span data-stu-id="21cc5-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="21cc5-526">Por ejemplo, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="21cc5-527">Objeto de valores de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-527">The route values object:</span></span>

* <span data-ttu-id="21cc5-528">Por convención suele ser un objeto de tipo anónimo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="21cc5-529">Puede ser `IDictionary<>` un poco o un [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="21cc5-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="21cc5-530">Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="21cc5-531">El código anterior `/Products/Buy/17?color=red`genera .</span><span class="sxs-lookup"><span data-stu-id="21cc5-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="21cc5-532">El código siguiente genera una dirección URL absoluta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="21cc5-533">Para crear una dirección URL absoluta, utilice una de las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="21cc5-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="21cc5-534">Una sobrecarga que `protocol`acepta un archivo .</span><span class="sxs-lookup"><span data-stu-id="21cc5-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="21cc5-535">Por ejemplo, el código anterior.</span><span class="sxs-lookup"><span data-stu-id="21cc5-535">For example, the preceding code.</span></span>
* <span data-ttu-id="21cc5-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que genera URI absolutos de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="21cc5-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="21cc5-537">Generar URL por ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-537">Generate URLs by route</span></span>

<span data-ttu-id="21cc5-538">El código anterior demostró la generación de una dirección URL pasando el controlador y el nombre de la acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="21cc5-539">`IUrlHelper`también proporciona la familia de métodos [Url.RouteUrl.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)</span><span class="sxs-lookup"><span data-stu-id="21cc5-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="21cc5-540">Estos métodos son similares a [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), pero `action` `controller` no copian los valores actuales de y a los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="21cc5-541">El uso más `Url.RouteUrl`común de :</span><span class="sxs-lookup"><span data-stu-id="21cc5-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="21cc5-542">Especifica un nombre de ruta para generar la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="21cc5-543">Por lo general, no especifica un controlador o un nombre de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="21cc5-544">El siguiente archivo Razor genera `Destination_Route`un enlace HTML a:</span><span class="sxs-lookup"><span data-stu-id="21cc5-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="21cc5-545">Generar URLs en HTML y Razor</span><span class="sxs-lookup"><span data-stu-id="21cc5-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="21cc5-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>proporciona <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> los métodos [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) y `<form>` [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para generar y `<a>` elementos respectivamente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="21cc5-547">Estos métodos usan el [método Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para generar una dirección URL y aceptan argumentos similares.</span><span class="sxs-lookup"><span data-stu-id="21cc5-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="21cc5-548">Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.</span><span class="sxs-lookup"><span data-stu-id="21cc5-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="21cc5-549">Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="21cc5-550">Ambos usan `IUrlHelper` para su implementación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="21cc5-551">Consulte [Aplicaciones auxiliares](xref:mvc/views/working-with-forms) de etiquetas en formularios para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="21cc5-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="21cc5-552">Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="21cc5-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="21cc5-553">Generación de URL en los resultados de la acción</span><span class="sxs-lookup"><span data-stu-id="21cc5-553">URL generation in Action Results</span></span>

<span data-ttu-id="21cc5-554">Los ejemplos anteriores `IUrlHelper` mostraban el uso en un controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="21cc5-555">El uso más común en un controlador es generar una dirección URL como parte de un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="21cc5-556">Las clases base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> y <xref:Microsoft.AspNetCore.Mvc.Controller> proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="21cc5-557">Un uso típico es redirigir después de aceptar la entrada del usuario:</span><span class="sxs-lookup"><span data-stu-id="21cc5-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="21cc5-558">La acción da como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> y sigue un patrón `IUrlHelper`similar a los métodos de .</span><span class="sxs-lookup"><span data-stu-id="21cc5-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="21cc5-559">Caso especial para rutas convencionales dedicadas</span><span class="sxs-lookup"><span data-stu-id="21cc5-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="21cc5-560">[El enrutamiento convencional](#cr) puede utilizar un tipo especial de definición de ruta denominada [ruta convencional dedicada.](#dcr)</span><span class="sxs-lookup"><span data-stu-id="21cc5-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="21cc5-561">En el ejemplo siguiente, `blog` la ruta denominada es una ruta convencional dedicada:</span><span class="sxs-lookup"><span data-stu-id="21cc5-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="21cc5-562">Utilizando las definiciones `Url.Action("Index", "Home")` de ruta `/` anteriores, genera la ruta URL mediante la `default` ruta, pero ¿por qué?</span><span class="sxs-lookup"><span data-stu-id="21cc5-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="21cc5-563">Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="21cc5-564">[Las rutas convencionales dedicadas](#dcr) se basan en un comportamiento especial de valores predeterminados que no tienen un parámetro de ruta correspondiente que impide que la ruta sea demasiado [expansiva](xref:fundamentals/routing#greedy) con la generación de URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="21cc5-565">En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="21cc5-566">Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="21cc5-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="21cc5-567">Se produce `blog` un error `{ controller = Home, action = Index }` en la `{ controller = Blog, action = Article }`generación de URL mediante porque los valores no coinciden con .</span><span class="sxs-lookup"><span data-stu-id="21cc5-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="21cc5-568">Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="21cc5-569">Áreas</span><span class="sxs-lookup"><span data-stu-id="21cc5-569">Areas</span></span>

<span data-ttu-id="21cc5-570">[Las áreas](xref:mvc/controllers/areas) son una característica MVC utilizada para organizar la funcionalidad relacionada en un grupo como un independiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="21cc5-571">Espacio de nombres de enrutamiento para acciones de controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="21cc5-572">Estructura de carpetas para vistas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-572">Folder structure for views.</span></span>

<span data-ttu-id="21cc5-573">El uso de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan áreas diferentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="21cc5-574">El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="21cc5-575">En esta sección se describe cómo interactúa el enrutamiento con las áreas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="21cc5-576">Consulte [Areas](xref:mvc/controllers/areas) para obtener más información sobre cómo se utilizan las áreas con las vistas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="21cc5-577">En el ejemplo siguiente se configura MVC `area` para utilizar `area` `Blog`la ruta convencional predeterminada y una ruta para un nombre:</span><span class="sxs-lookup"><span data-stu-id="21cc5-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="21cc5-578">En el código <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> anterior, se `"blog_route"`llama para crear el archivo .</span><span class="sxs-lookup"><span data-stu-id="21cc5-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="21cc5-579">El segundo `"Blog"`parámetro, , es el nombre del área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="21cc5-580">Al hacer coincidir `/Manage/Users/AddUser`una `"blog_route"` ruta URL como `{ area = Blog, controller = Users, action = AddUser }`, la ruta genera los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="21cc5-581">El `area` valor de ruta se produce `area`mediante un valor predeterminado para .</span><span class="sxs-lookup"><span data-stu-id="21cc5-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="21cc5-582">La ruta `MapAreaControllerRoute` creada por es equivalente a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="21cc5-583">`MapAreaControllerRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="21cc5-584">El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="21cc5-585">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="21cc5-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="21cc5-586">En general, las rutas con áreas deben colocarse antes, ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="21cc5-587">Utilizando el ejemplo anterior, `{ area = Blog, controller = Users, action = AddUser }` los valores de ruta coinciden con la acción siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="21cc5-588">El atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) es lo que denota un controlador como parte de un área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="21cc5-589">Este controlador está `Blog` en el área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="21cc5-590">Los reguladores sin un `[Area]` atributo no son miembros de ninguna área, y **no** hacen juego cuando el valor de ruta es proporcionado por el `area` ruteo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="21cc5-591">En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="21cc5-592">El espacio de nombres de cada controlador se muestra aquí para su integridad.</span><span class="sxs-lookup"><span data-stu-id="21cc5-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="21cc5-593">Si los controladores anteriores usan el mismo espacio de nombres, se generaría un error del compilador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="21cc5-594">Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.</span><span class="sxs-lookup"><span data-stu-id="21cc5-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="21cc5-595">Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="21cc5-596">El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="21cc5-597">En términos de búsqueda de coincidencias de *ningún valor*, la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.</span><span class="sxs-lookup"><span data-stu-id="21cc5-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="21cc5-598">Al ejecutar una acción dentro de un `area` área, el valor de ruta para está disponible como [un valor ambiente](#ambient) para el enrutamiento que se usará para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="21cc5-599">Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="21cc5-600">El código siguiente genera `/Zebra/Users/AddUser`una dirección URL a:</span><span class="sxs-lookup"><span data-stu-id="21cc5-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="21cc5-601">Definición de acción</span><span class="sxs-lookup"><span data-stu-id="21cc5-601">Action definition</span></span>

<span data-ttu-id="21cc5-602">Los métodos públicos en un controlador, excepto aquellos con el [nonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) atributo, son acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="21cc5-603">Código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="21cc5-603">Sample code</span></span>

 * <span data-ttu-id="21cc5-604">El [método MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) se incluye en la descarga de [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) y se usa para mostrar información de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="21cc5-605">[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ( cómo[descargar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="21cc5-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="21cc5-606">ASP.NET Core MVC utiliza el [middleware](xref:fundamentals/middleware/index) de enrutamiento para buscar las direcciones URL de las solicitudes entrantes y asignarlas a acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="21cc5-607">Las rutas se definen en el código de inicio o los atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="21cc5-608">Las rutas describen cómo se deben asociar las rutas de dirección URL a las acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="21cc5-609">Las rutas también se usan para generar direcciones URL (para vínculos) enviadas en las respuestas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="21cc5-610">Las acciones se enrutan bien mediante convención o bien mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="21cc5-611">Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="21cc5-612">Consulte [Enrutamiento mixto](#routing-mixed-ref-label) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="21cc5-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="21cc5-613">Este documento explica las interacciones entre MVC y el enrutamiento, así como el uso que las aplicaciones MVC suelen hacer de las características de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="21cc5-614">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener más información sobre enrutamiento avanzado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="21cc5-615">Configurar el middleware de enrutamiento</span><span class="sxs-lookup"><span data-stu-id="21cc5-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="21cc5-616">En su método *Configure* puede ver código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="21cc5-617">Dentro de la llamada a `UseMvc`, `MapRoute` se utiliza para crear una ruta única, a la que nos referiremos como la ruta `default`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="21cc5-618">La mayoría de las aplicaciones MVC usarán una ruta con una plantilla similar a la ruta `default`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="21cc5-619">La plantilla de ruta `"{controller=Home}/{action=Index}/{id?}"` puede coincidir con una ruta de dirección URL como `/Products/Details/5` y extraerá los valores de ruta `{ controller = Products, action = Details, id = 5 }` mediante la conversión de la ruta en tokens.</span><span class="sxs-lookup"><span data-stu-id="21cc5-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="21cc5-620">MVC intentará encontrar un controlador denominado `ProductsController` y ejecutar la acción `Details`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="21cc5-621">Tenga en cuenta que, en este ejemplo, el enlace de modelos usaría el valor de `id = 5` para establecer el parámetro `id` en `5` al invocar esta acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="21cc5-622">Consulte [Enlace de modelos](../models/model-binding.md) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="21cc5-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="21cc5-623">Utilizando la ruta `default`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="21cc5-624">La plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-624">The route template:</span></span>

* <span data-ttu-id="21cc5-625">`{controller=Home}` define `Home` como el valor `controller` predeterminado</span><span class="sxs-lookup"><span data-stu-id="21cc5-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="21cc5-626">`{action=Index}` define `Index` como el valor `action` predeterminado</span><span class="sxs-lookup"><span data-stu-id="21cc5-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="21cc5-627">`{id?}` define `id` como opcional</span><span class="sxs-lookup"><span data-stu-id="21cc5-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="21cc5-628">No es necesario que los parámetros de ruta opcionales y predeterminados estén presentes en la ruta de dirección URL para una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="21cc5-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="21cc5-629">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="21cc5-630">`"{controller=Home}/{action=Index}/{id?}"` puede coincidir con la ruta de dirección URL `/` y generará los valores de ruta `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="21cc5-631">Los valores de `controller` y `action` utilizan los valores predeterminados, `id` no genera un valor porque no hay ningún segmento correspondiente en la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="21cc5-632">MVC utilizaría estos valores de ruta para seleccionar `HomeController` y la acción `Index`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="21cc5-633">Usando esta definición de controlador y la plantilla de ruta, la acción `HomeController.Index` se ejecutaría para cualquiera de las rutas de dirección URL siguientes:</span><span class="sxs-lookup"><span data-stu-id="21cc5-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="21cc5-634">El método de conveniencia `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="21cc5-635">Se puede usar para reemplazar:</span><span class="sxs-lookup"><span data-stu-id="21cc5-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="21cc5-636">`UseMvc` y `UseMvcWithDefaultRoute` agregan una instancia de `RouterMiddleware` a la canalización de middleware.</span><span class="sxs-lookup"><span data-stu-id="21cc5-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="21cc5-637">MVC no interactúa directamente con middleware y usa el enrutamiento para controlar las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="21cc5-638">MVC está conectado a las rutas a través de una instancia de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="21cc5-639">El código dentro de `UseMvc` es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="21cc5-640">`UseMvc` no define directamente ninguna ruta, sino que agrega un marcador de posición a la colección de rutas para la ruta `attribute`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="21cc5-641">La sobrecarga `UseMvc(Action<IRouteBuilder>)` le permite agregar sus propias rutas y también admite el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="21cc5-642">`UseMvc` y todas sus variaciones agregan un marcador de posición para la ruta de atributo (el enrutamiento mediante atributos siempre está disponible, independientemente de cómo configure `UseMvc`).</span><span class="sxs-lookup"><span data-stu-id="21cc5-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="21cc5-643">`UseMvcWithDefaultRoute` define una ruta predeterminada y admite el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="21cc5-644">La sección [Enrutamiento mediante atributos](#attribute-routing-ref-label) incluye más detalles sobre este tipo de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="21cc5-645">Enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="21cc5-645">Conventional routing</span></span>

<span data-ttu-id="21cc5-646">La ruta `default`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="21cc5-647">El código anterior es un ejemplo de un enrutamiento convencional.</span><span class="sxs-lookup"><span data-stu-id="21cc5-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="21cc5-648">Este estilo se denomina enrutamiento convencional porque establece una *convención* para las rutas de URL:</span><span class="sxs-lookup"><span data-stu-id="21cc5-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="21cc5-649">El primer segmento de ruta se asigna al nombre del controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="21cc5-650">El segundo se asigna al nombre de la acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-650">The second maps to the action name.</span></span>
* <span data-ttu-id="21cc5-651">El tercer segmento se `id`utiliza para un archivo .</span><span class="sxs-lookup"><span data-stu-id="21cc5-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="21cc5-652">`id`se asigna a una entidad de modelo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="21cc5-653">Mediante esta ruta `default`, la ruta de dirección URL `/Products/List` se asigna a la acción `ProductsController.List`, y `/Blog/Article/17` se asigna a `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="21cc5-654">Esta asignación **solo** se basa en los nombres de acción y controlador, y no en espacios de nombres, ubicaciones de archivos de origen ni parámetros de método.</span><span class="sxs-lookup"><span data-stu-id="21cc5-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="21cc5-655">Usar el enrutamiento convencional con la ruta predeterminada permite generar rápidamente la aplicación sin necesidad de elaborar un nuevo patrón de dirección URL para cada acción que se define.</span><span class="sxs-lookup"><span data-stu-id="21cc5-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="21cc5-656">Para una aplicación con acciones de estilo CRUD, la coherencia en las direcciones URL en todos los controladores puede ayudar a simplificar el código y hacer que la interfaz de usuario sea más predecible.</span><span class="sxs-lookup"><span data-stu-id="21cc5-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="21cc5-657">`id` se define como opcional mediante la plantilla de ruta, lo que significa que las acciones se pueden ejecutar sin el identificador proporcionado como parte de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="21cc5-658">Normalmente lo que ocurrirá si `id` se omite de la dirección URL es que el enlace de modelos establecerá en `0` su valor y, como resultado, no se encontrará ninguna entidad en la base de datos que coincida con `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="21cc5-659">El enrutamiento mediante atributos proporciona un mayor control que permite requerir el identificador para algunas acciones y para otras no.</span><span class="sxs-lookup"><span data-stu-id="21cc5-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="21cc5-660">Por convención, la documentación incluirá parámetros opcionales como `id` cuando sea más probable que su uso sea correcto.</span><span class="sxs-lookup"><span data-stu-id="21cc5-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="21cc5-661">Varias rutas</span><span class="sxs-lookup"><span data-stu-id="21cc5-661">Multiple routes</span></span>

<span data-ttu-id="21cc5-662">Para agregar varias rutas dentro de `UseMvc`, agregue más llamadas a `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="21cc5-663">Hacerlo le permite definir varias convenciones o agregar rutas convencionales que se dedican a una acción específica, como en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="21cc5-664">Aquí, la ruta `blog` es una *ruta convencional dedicada*, lo que significa que utiliza el sistema de enrutamiento convencional, pero que está dedicada a una acción específica.</span><span class="sxs-lookup"><span data-stu-id="21cc5-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="21cc5-665">Puesto que `controller` y `action` no aparecen en la plantilla de ruta como parámetros, solo pueden tener los valores predeterminados y, por tanto, esta ruta siempre se asignará a la acción `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="21cc5-666">Las rutas de la colección de rutas están ordenadas y se procesan en el orden en que se hayan agregado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="21cc5-667">Por tanto, la ruta `blog` de este ejemplo se intentará antes que la ruta `default`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-668">*Las rutas convencionales dedicadas* a menudo `{*article}` utilizan parámetros de ruta **catch-all** como para capturar la parte restante de la ruta URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="21cc5-669">Esto puede hacer que la ruta sea "demasiado expansiva", lo que significa que coincidirá con direcciones URL que se pretendía que coincidieran con otras rutas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="21cc5-670">Coloque las rutas "expansivas" más adelante en la tabla de rutas para resolver este problema.</span><span class="sxs-lookup"><span data-stu-id="21cc5-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="21cc5-671">Reserva</span><span class="sxs-lookup"><span data-stu-id="21cc5-671">Fallback</span></span>

<span data-ttu-id="21cc5-672">Como parte del procesamiento de la solicitud, MVC comprobará que los valores de ruta se pueden usar para buscar un controlador y la acción en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="21cc5-673">Si los valores de ruta no coinciden con una acción, entonces la ruta no se considera una coincidencia y se intentará la ruta siguiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="21cc5-674">Esto se denomina *reserva*, y se ha diseñado para simplificar los casos donde se superponen rutas convencionales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="21cc5-675">Eliminar la ambigüedad de acciones</span><span class="sxs-lookup"><span data-stu-id="21cc5-675">Disambiguating actions</span></span>

<span data-ttu-id="21cc5-676">Cuando dos acciones coinciden en el enrutamiento, MVC debe eliminar la ambigüedad para elegir el candidato "recomendado", o de lo contrario se produce una excepción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="21cc5-677">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="21cc5-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="21cc5-678">Este controlador define dos acciones que coincidirían con la ruta de dirección URL `/Products/Edit/17` y los datos de ruta `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="21cc5-679">Se trata de un patrón típico para controladores MVC donde `Edit(int)` muestra un formulario para editar un producto, y `Edit(int, Product)` procesa el formulario expuesto.</span><span class="sxs-lookup"><span data-stu-id="21cc5-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="21cc5-680">Para hacer esto posible, MVC necesita seleccionar `Edit(int, Product)` cuando la solicitud es HTTP `POST` y `Edit(int)` cuando el verbo HTTP es cualquier otra cosa.</span><span class="sxs-lookup"><span data-stu-id="21cc5-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="21cc5-681">`HttpPostAttribute` ( `[HttpPost]` ) es una implementación de `IActionConstraint` que solo permitirá que la acción se seleccione cuando el verbo HTTP sea `POST`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="21cc5-682">La presencia de `IActionConstraint` hace que `Edit(int, Product)` sea una mejor coincidencia que `Edit(int)`, por lo que `Edit(int, Product)` se intentará en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="21cc5-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="21cc5-683">Solo necesitará escribir implementaciones de `IActionConstraint` personalizadas en escenarios especializados, pero es importante comprender el rol de atributos como `HttpPostAttribute` (para otros verbos HTTP se definen atributos similares).</span><span class="sxs-lookup"><span data-stu-id="21cc5-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="21cc5-684">En el enrutamiento convencional es común que las acciones utilicen el mismo nombre de acción cuando son parte de un flujo de trabajo `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="21cc5-685">La comodidad de este patrón será más evidente después de revisar la sección [Descripción de IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="21cc5-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="21cc5-686">Si coinciden varias rutas y MVC no encuentra una ruta "recomendada", se produce una excepción `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="21cc5-687">Nombres de ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-687">Route names</span></span>

<span data-ttu-id="21cc5-688">Las cadenas `"blog"` y `"default"` de los ejemplos siguientes son nombres de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="21cc5-689">Los nombres de ruta proporcionan un nombre lógico a la ruta para que la ruta con nombre pueda utilizarse en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="21cc5-690">Esto simplifica en gran medida la creación de direcciones URL en los casos en que el orden de las rutas podría complicar la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="21cc5-691">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="21cc5-692">Los nombres de ruta no tienen ningún impacto en la coincidencia de direcciones URL ni en el control de las solicitudes; se utilizan únicamente para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="21cc5-693">En [Enrutamiento](xref:fundamentals/routing) se incluye información más detallada sobre la generación de direcciones URL, como la generación de direcciones URL en asistentes específicos de MVC.</span><span class="sxs-lookup"><span data-stu-id="21cc5-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="21cc5-694">Enrutamiento mediante atributos</span><span class="sxs-lookup"><span data-stu-id="21cc5-694">Attribute routing</span></span>

<span data-ttu-id="21cc5-695">El enrutamiento mediante atributos utiliza un conjunto de atributos para asignar acciones directamente a las plantillas de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="21cc5-696">En el ejemplo siguiente, `app.UseMvc();` se utiliza en el método `Configure` y no se pasa ninguna ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="21cc5-697">`HomeController` coincidirá con un conjunto de direcciones URL similares a las que coincidirían con la ruta predeterminada `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="21cc5-698">La acción `HomeController.Index()` se ejecutará para cualquiera de las rutas de dirección URL `/`, `/Home` o `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-699">Este ejemplo resalta una diferencia clave de programación entre el enrutamiento mediante atributos y el enrutamiento convencional.</span><span class="sxs-lookup"><span data-stu-id="21cc5-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="21cc5-700">El enrutamiento mediante atributos requiere más entradas para especificar una ruta; la ruta predeterminada convencional controla las rutas de manera más sucinta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="21cc5-701">Pero el enrutamiento mediante atributos permite (y requiere) un control preciso de las plantillas de ruta que se aplicarán a cada acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="21cc5-702">Con el enrutamiento mediante atributos, el nombre del controlador y los nombres de acción **no** juegan ningún papel en la selección de las acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="21cc5-703">Este ejemplo coincidirá con las mismas direcciones URL que el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="21cc5-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="21cc5-704">Las plantillas de ruta anteriores no definen parámetros de ruta para `action`, `area` y `controller`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="21cc5-705">De hecho, estos parámetros de ruta no se permiten en rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="21cc5-706">Puesto que la plantilla de ruta ya está asociada a una acción, no tendría sentido analizar el nombre de acción de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="21cc5-707">Enrutamiento mediante atributos con atributos Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="21cc5-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="21cc5-708">El enrutamiento mediante atributos también puede hacer uso de los atributos `Http[Verb]` como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="21cc5-709">Todos estos atributos pueden aceptar una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="21cc5-710">Este ejemplo muestra dos acciones que coinciden con la misma plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="21cc5-711">Para una ruta de dirección URL como `/products`, la acción `ProductsApi.ListProducts` se ejecutará cuando el verbo HTTP sea `GET` y `ProductsApi.CreateProduct` se ejecutará cuando el verbo HTTP sea `POST`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="21cc5-712">El enrutamiento mediante atributos primero busca una coincidencia de la dirección URL en el conjunto de plantillas de ruta que se definen mediante atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="21cc5-713">Una vez que se encuentra una plantilla de ruta que coincide, las restricciones `IActionConstraint` se aplican para determinar qué acciones se pueden ejecutar.</span><span class="sxs-lookup"><span data-stu-id="21cc5-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="21cc5-714">Cuando se compila una API de REST, es poco frecuente que se quiera usar `[Route(...)]` en un método de acción, ya que la acción aceptará todos los métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="21cc5-715">Es mejor usar `Http*Verb*Attributes` más específicos para precisar lo que es compatible con la API.</span><span class="sxs-lookup"><span data-stu-id="21cc5-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="21cc5-716">Se espera que los clientes de API de REST sepan qué rutas y verbos HTTP se asignan a determinadas operaciones lógicas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="21cc5-717">Puesto que una ruta de atributo se aplica a una acción específica, es fácil crear parámetros necesarios como parte de la definición de plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="21cc5-718">En este ejemplo, se requiere `id` como parte de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="21cc5-719">La acción `ProductsApi.GetProduct(int)` se ejecutará para una ruta de dirección URL como `/products/3`, pero no para una ruta de dirección URL como `/products`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="21cc5-720">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción completa de las plantillas de ruta y las opciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="21cc5-721">Nombre de ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-721">Route Name</span></span>

<span data-ttu-id="21cc5-722">El código siguiente define un *nombre de ruta* de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="21cc5-723">Los nombres de ruta se pueden utilizar para generar una dirección URL basada en una ruta específica.</span><span class="sxs-lookup"><span data-stu-id="21cc5-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="21cc5-724">Los nombres de ruta no tienen ningún impacto en el comportamiento de coincidencia de direcciones URL del enrutamiento, y solo se usan para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="21cc5-725">Los nombres de ruta deben ser únicos en toda la aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-726">Compare esto con la *ruta predeterminada* convencional, que define el parámetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="21cc5-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="21cc5-727">Esta capacidad de especificar con precisión las API tiene sus ventajas, como permitir que `/products` y `/products/5` se envíen a acciones diferentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="21cc5-728">Combinación de rutas</span><span class="sxs-lookup"><span data-stu-id="21cc5-728">Combining routes</span></span>

<span data-ttu-id="21cc5-729">Para que el enrutamiento mediante atributos sea menos repetitivo, los atributos de ruta del controlador se combinan con los atributos de ruta de las acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="21cc5-730">Las plantillas de ruta definidas en el controlador se anteponen a las plantillas de ruta de las acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="21cc5-731">La colocación de un atributo de ruta en el controlador hace que **todas** las acciones del controlador usen el enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="21cc5-732">En este ejemplo, la ruta de dirección URL `/products` puede coincidir con `ProductsApi.ListProducts` y la ruta de dirección URL `/products/5` puede coincidir con `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="21cc5-733">Ambas acciones solo coinciden con el método HTTP `GET` porque están marcadas con `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="21cc5-734">Las plantillas de ruta aplicadas a una acción que comienzan por `/` o `~/` no se combinan con las plantillas de ruta que se aplican al controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="21cc5-735">En este ejemplo coinciden un conjunto de rutas de dirección URL similares a la *ruta predeterminada*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-735">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="21cc5-736">Ordenación de rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="21cc5-736">Ordering attribute routes</span></span>

<span data-ttu-id="21cc5-737">A diferencia de las rutas convencionales, que se ejecutan en un orden definido, el enrutamiento de atributos crea un árbol y coincide con todas las rutas simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="21cc5-738">Es como si las entradas de ruta se colocasen en un orden ideal; las rutas más específicas tienen la oportunidad de ejecutarse antes que las rutas más generales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="21cc5-739">Por ejemplo, una ruta como `blog/search/{topic}` es más específica que una ruta como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="21cc5-740">Desde un punto de vista lógico, la ruta `blog/search/{topic}` se ejecuta en primer lugar de forma predeterminada, ya que ese es el único orden significativo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="21cc5-741">En el enrutamiento convencional, el desarrollador es responsable de colocar las rutas en el orden deseado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="21cc5-742">Las rutas de atributo pueden configurar un orden mediante la propiedad `Order` de todos los atributos de ruta proporcionados por el marco.</span><span class="sxs-lookup"><span data-stu-id="21cc5-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="21cc5-743">Las rutas se procesan de acuerdo con el orden ascendente de la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="21cc5-744">El orden predeterminado es `0`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-744">The default order is `0`.</span></span> <span data-ttu-id="21cc5-745">Si una ruta se configura con `Order = -1`, se ejecutará antes que las rutas que establecen un orden.</span><span class="sxs-lookup"><span data-stu-id="21cc5-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="21cc5-746">Si una ruta se configura con `Order = 1`, se ejecutará después del orden de rutas predeterminado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="21cc5-747">Evite depender de `Order`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="21cc5-748">Si su espacio de direcciones URL requiere unos valores de orden explícitos para un enrutamiento correcto, es probable que también sea confuso para los clientes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="21cc5-749">Por lo general, el enrutamiento mediante atributos seleccionará la ruta correcta con la coincidencia de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="21cc5-750">Si el orden predeterminado que se usa para la generación de direcciones URL no funciona, normalmente es más sencillo utilizar el nombre de ruta como una invalidación que aplicar la propiedad `Order`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="21cc5-751">El enrutamiento del controlador de MVC y el enrutamiento de Razor Pages comparten una implementación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="21cc5-752">La información sobre el orden de la ruta de los temas de Razor Pages se encuentra disponible en [Convenciones de aplicación y de ruta de Razor Pages: Orden de la ruta](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="21cc5-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="21cc5-753">Reemplazo de tokens en plantillas de ruta ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="21cc5-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="21cc5-754">Para mayor comodidad, las rutas de atributos admiten el`[` `]`reemplazo de *tokens* encerrando un token en corchetes ( , ).</span><span class="sxs-lookup"><span data-stu-id="21cc5-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="21cc5-755">Los tokens `[action]`, `[area]` y `[controller]` se reemplazan con los valores del nombre de la acción, el nombre del área y el nombre del controlador de la acción donde se define la ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="21cc5-756">En este ejemplo, las acciones coinciden con las rutas de dirección URL, tal como se describe en los comentarios:</span><span class="sxs-lookup"><span data-stu-id="21cc5-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="21cc5-757">El reemplazo del token se produce como último paso de la creación de las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="21cc5-758">El ejemplo anterior se comportará igual que el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="21cc5-759">Las rutas de atributo también se pueden combinar con la herencia.</span><span class="sxs-lookup"><span data-stu-id="21cc5-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="21cc5-760">Esto es especialmente eficaz si se combina con el reemplazo de token.</span><span class="sxs-lookup"><span data-stu-id="21cc5-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="21cc5-761">El reemplazo de token también se aplica a los nombres de ruta definidos por las rutas de atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="21cc5-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nombre de ruta único para cada acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="21cc5-763">Para que el delimitador de reemplazo de token `[` o `]` coincida, repita el carácter (`[[` o `]]`) para usarlo como secuencia de escape.</span><span class="sxs-lookup"><span data-stu-id="21cc5-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="21cc5-764">Usar un transformador de parámetro para personalizar el reemplazo de tokens</span><span class="sxs-lookup"><span data-stu-id="21cc5-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="21cc5-765">El reemplazo de tokens se puede personalizarse mediante un transformador de parámetro.</span><span class="sxs-lookup"><span data-stu-id="21cc5-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="21cc5-766">Un transformador de parámetro implementa `IOutboundParameterTransformer` y transforma el valor de parámetros.</span><span class="sxs-lookup"><span data-stu-id="21cc5-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="21cc5-767">Por ejemplo, un transformador de parámetros `SlugifyParameterTransformer` personalizado cambia el valor de ruta `SubscriptionManagement` a `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="21cc5-768">`RouteTokenTransformerConvention` es una convención de modelo de aplicación que:</span><span class="sxs-lookup"><span data-stu-id="21cc5-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="21cc5-769">Aplica un transformador de parámetro a todas las rutas de atributo en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="21cc5-770">Personaliza los valores de token de ruta de atributo que se reemplazan.</span><span class="sxs-lookup"><span data-stu-id="21cc5-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="21cc5-771">`RouteTokenTransformerConvention` está registrado como una opción en `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="21cc5-772">Varias rutas</span><span class="sxs-lookup"><span data-stu-id="21cc5-772">Multiple Routes</span></span>

<span data-ttu-id="21cc5-773">El enrutamiento mediante atributos permite definir varias rutas que llegan a la misma acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="21cc5-774">El uso más común de esto es imitar el comportamiento de la *ruta convencional predeterminada* tal como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="21cc5-775">La colocación de varios atributos de ruta en el controlador significa que cada uno de ellos se combinará con cada uno de los atributos de ruta en los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="21cc5-776">Cuando en una acción se colocan varios atributos de ruta (que implementan `IActionConstraint`), cada restricción de acción se combina con la plantilla de ruta del atributo que lo define.</span><span class="sxs-lookup"><span data-stu-id="21cc5-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="21cc5-777">Si bien el uso de varias rutas en acciones puede parecer eficaz, es mejor que el espacio de direcciones URL de la aplicación sea sencillo y esté bien definido.</span><span class="sxs-lookup"><span data-stu-id="21cc5-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="21cc5-778">Utilice varias rutas en acciones solo cuando sea necesario, por ejemplo, para admitir a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="21cc5-779">Especificación de parámetros opcionales de ruta de atributo, valores predeterminados y restricciones</span><span class="sxs-lookup"><span data-stu-id="21cc5-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="21cc5-780">Las rutas de atributo admiten la misma sintaxis en línea que las rutas convencionales para especificar parámetros opcionales, valores predeterminados y restricciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="21cc5-781">Consulte [Referencia de plantilla de ruta](xref:fundamentals/routing#route-template-reference) para obtener una descripción detallada de la sintaxis de la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="21cc5-782">Atributos de ruta personalizados con `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="21cc5-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="21cc5-783">Todos los atributos de ruta proporcionados en el marco (`[Route(...)]`, `[HttpGet(...)]`, etc.) implementan la interfaz `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="21cc5-784">Al iniciarse la aplicación, MVC busca atributos en las clases de controlador y los métodos de acción, y usa los que implementan `IRouteTemplateProvider` para generar el conjunto inicial de rutas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="21cc5-785">Implemente `IRouteTemplateProvider` para definir sus propios atributos de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="21cc5-786">Cada `IRouteTemplateProvider` le permite definir una única ruta con una plantilla de ruta, un orden y un nombre personalizados:</span><span class="sxs-lookup"><span data-stu-id="21cc5-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="21cc5-787">El atributo del ejemplo anterior establece automáticamente `Template` en `"api/[controller]"` cuando se aplica `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="21cc5-788">Uso del modelo de aplicación para personalizar las rutas de atributo</span><span class="sxs-lookup"><span data-stu-id="21cc5-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="21cc5-789">El *modelo de aplicación* es un modelo de objetos creado durante el inicio con todos los metadatos utilizados por MVC para enrutar y ejecutar las acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="21cc5-790">El *modelo de aplicación* incluye todos los datos recopilados de los atributos de ruta (a través de `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="21cc5-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="21cc5-791">Puede escribir *convenciones* para modificar el modelo de aplicación en el momento del inicio y personalizar el comportamiento del enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="21cc5-792">En esta sección se muestra un ejemplo sencillo de personalización del enrutamiento mediante el modelo de aplicación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="21cc5-793">Enrutamiento mixto: enrutamiento mediante atributos frente a enrutamiento convencional</span><span class="sxs-lookup"><span data-stu-id="21cc5-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="21cc5-794">Las aplicaciones MVC pueden combinar el uso de enrutamiento convencional y enrutamiento mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="21cc5-795">Es habitual usar las rutas convencionales para controladores que sirven páginas HTML para los exploradores, y el enrutamiento mediante atributos para los controladores que sirven las API de REST.</span><span class="sxs-lookup"><span data-stu-id="21cc5-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="21cc5-796">Las acciones se enrutan bien mediante convención o bien mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="21cc5-797">Colocar una ruta en el controlador o la acción hace que se enrute mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="21cc5-798">Las acciones que definen rutas de atributo no se pueden alcanzar a través de las rutas convencionales y viceversa.</span><span class="sxs-lookup"><span data-stu-id="21cc5-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="21cc5-799">**Cualquier** atributo de ruta en el controlador hace que todas las acciones del controlador se enruten mediante atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-800">Lo que distingue los dos tipos de sistemas de enrutamiento es el proceso que se aplica después de que una dirección URL coincida con una plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="21cc5-801">En el enrutamiento convencional, los valores de ruta de la coincidencia se usan para elegir la acción y el controlador en una tabla de búsqueda de todas las acciones enrutadas convencionales.</span><span class="sxs-lookup"><span data-stu-id="21cc5-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="21cc5-802">En el enrutamiento mediante atributos, cada plantilla ya está asociada a una acción y no es necesaria ninguna otra búsqueda.</span><span class="sxs-lookup"><span data-stu-id="21cc5-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="21cc5-803">Segmentos complejos</span><span class="sxs-lookup"><span data-stu-id="21cc5-803">Complex segments</span></span>

<span data-ttu-id="21cc5-804">Los segmentos complejos (por ejemplo, `[Route("/dog{token}cat")]`), se procesan buscando coincidencias de literales de derecha a izquierda de un modo no expansivo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="21cc5-805">Para ver una descripción, eche un vistazo al [código fuente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="21cc5-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="21cc5-806">Para más información, vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="21cc5-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="21cc5-807">Generación de direcciones URL</span><span class="sxs-lookup"><span data-stu-id="21cc5-807">URL Generation</span></span>

<span data-ttu-id="21cc5-808">Las aplicaciones MVC pueden usar características de generación de direcciones URL de enrutamiento para generar vínculos URL a las acciones.</span><span class="sxs-lookup"><span data-stu-id="21cc5-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="21cc5-809">La generación de direcciones URL elimina las direcciones URL codificadas de forma rígida, por lo que el código es más compacto y fácil de mantener.</span><span class="sxs-lookup"><span data-stu-id="21cc5-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="21cc5-810">Esta sección se centra en las características de generación de direcciones URL proporcionadas por MVC y solo aborda los conceptos básicos de su funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="21cc5-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="21cc5-811">Consulte [Enrutamiento](xref:fundamentals/routing) para obtener una descripción detallada de la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="21cc5-812">La interfaz `IUrlHelper` es la pieza subyacente de la infraestructura entre MVC y el enrutamiento para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="21cc5-813">Encontrará una instancia de `IUrlHelper` disponible a través de la propiedad `Url` en los controladores, las vistas y los componentes de vista.</span><span class="sxs-lookup"><span data-stu-id="21cc5-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="21cc5-814">En este ejemplo, la interfaz `IUrlHelper` se usa a través de la propiedad `Controller.Url` para generar una dirección URL a otra acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="21cc5-815">Si la aplicación está usando la ruta convencional predeterminada, el valor de la variable `url` será la cadena de ruta de dirección URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="21cc5-816">Esta ruta de dirección URL se crea por enrutamiento mediante la combinación de los valores de ruta de la solicitud actual (valores de ambiente) con los valores pasados a `Url.Action`, y sustituyendo esos valores en la plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="21cc5-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="21cc5-817">El valor de cada uno de los parámetros de ruta incluidos en la plantilla de ruta se sustituye por nombres que coincidan con los valores y los valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="21cc5-818">Si un parámetro de ruta no tiene un valor, puede utilizar un valor predeterminado en caso de tenerlo, o se puede omitir si es opcional (como en el caso de `id` en este ejemplo).</span><span class="sxs-lookup"><span data-stu-id="21cc5-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="21cc5-819">La generación de direcciones URL producirá un error si cualquiera de los parámetros de ruta necesarios no tiene un valor correspondiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="21cc5-820">Si se produce un error en la generación de direcciones URL para una ruta, se prueba con la ruta siguiente hasta que se hayan probado todas las rutas o se encuentra una coincidencia.</span><span class="sxs-lookup"><span data-stu-id="21cc5-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="21cc5-821">En el ejemplo anterior de `Url.Action` se supone que el enrutamiento es convencional, pero la generación de direcciones URL funciona del mismo modo con el enrutamiento mediante atributos, si bien los conceptos son diferentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="21cc5-822">En el enrutamiento convencional, los valores de ruta se usan para expandir una plantilla; los valores de ruta de `controller` y `action` suelen aparecer en esa plantilla. Esto es válido porque las direcciones URL que coinciden con el enrutamiento se adhieren a una *convención*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="21cc5-823">En el enrutamiento mediante atributos, no está permitido que los valores de ruta de `controller` y `action` aparezcan en la plantilla. En vez de eso, se utilizan para buscar la plantilla que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="21cc5-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="21cc5-824">Este ejemplo utiliza la enrutamiento mediante atributos:</span><span class="sxs-lookup"><span data-stu-id="21cc5-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="21cc5-825">MVC genera una tabla de búsqueda de todas las acciones enrutadas mediante atributos y hará coincidir los valores `controller` y `action` para seleccionar la plantilla de ruta que se usará para la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="21cc5-826">En el ejemplo anterior se genera `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="21cc5-827">Generación de direcciones URL por nombre de acción</span><span class="sxs-lookup"><span data-stu-id="21cc5-827">Generating URLs by action name</span></span>

<span data-ttu-id="21cc5-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="21cc5-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="21cc5-829">`Action`) y todas las sobrecargas relacionadas se basan en la idea de especificar el destino del vínculo mediante un nombre de controlador y un nombre de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-830">Cuando se usa `Url.Action`, se especifican los valores actuales de la ruta para `controller` y `action`. Los valores de `controller` y `action` forman parte tanto de los *valores de ambiente* \*\*y como de los \*\* *valores*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="21cc5-831">El método `Url.Action` siempre utiliza los valores actuales de `action` y `controller` y genera una ruta de dirección URL que dirige a la acción actual.</span><span class="sxs-lookup"><span data-stu-id="21cc5-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="21cc5-832">Intentos de enrutamiento para utilizar los valores en los valores de ambiente para rellenar la información que no se proporcionó al generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="21cc5-833">Al utilizar una ruta como `{a}/{b}/{c}/{d}` y los valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, el enrutamiento tiene suficiente información para generar una dirección URL sin ningún valor adicional, puesto que todos los parámetros de ruta tienen un valor.</span><span class="sxs-lookup"><span data-stu-id="21cc5-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="21cc5-834">Si se agregó el valor `{ d = Donovan }`, el valor `{ d = David }` se ignorará y la ruta de dirección URL generada será `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="21cc5-835">Las rutas de dirección URL son jerárquicas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-835">URL paths are hierarchical.</span></span> <span data-ttu-id="21cc5-836">En el ejemplo anterior, si se agrega el valor `{ c = Cheryl }`, ambos valores `{ c = Carol, d = David }` se ignorarán.</span><span class="sxs-lookup"><span data-stu-id="21cc5-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="21cc5-837">En este caso ya no tenemos un valor para `d` y se producirá un error en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="21cc5-838">Debe especificar el valor deseado de `c` y `d`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="21cc5-839">Es probable que este problema se produzca con la ruta predeterminada (`{controller}/{action}/{id?}`), pero raramente encontrará este comportamiento en la práctica, dado que `Url.Action` especificará siempre de manera explícita un valor `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="21cc5-840">Las sobrecargas más largas de `Url.Action` también toman un objeto de *valores de ruta* adicional para proporcionar valores para parámetros de ruta distintos de `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="21cc5-841">Normalmente verá esto utilizado con `id`, como `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="21cc5-842">Por convención, el objeto de *valores de ruta* normalmente es un objeto de tipo anónimo, pero también puede ser `IDictionary<>` o un *objeto .NET estándar*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="21cc5-843">Los valores de ruta adicionales que no coinciden con los parámetros de ruta se colocan en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="21cc5-844">Para crear una dirección URL absoluta, use una sobrecarga que acepte `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="21cc5-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="21cc5-845">Generación de direcciones URL por ruta</span><span class="sxs-lookup"><span data-stu-id="21cc5-845">Generating URLs by route</span></span>

<span data-ttu-id="21cc5-846">En el código anterior se pasó el nombre de acción y de controlador para generar una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="21cc5-847">`IUrlHelper` también proporciona la familia de métodos `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="21cc5-848">Estos métodos son similares a `Url.Action`, pero no copian los valores actuales de `action` y `controller` en los valores de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="21cc5-849">Lo más común es especificar un nombre de ruta para utilizar una ruta específica y generar la dirección URL, por lo general *sin* especificar un nombre de acción o controlador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="21cc5-850">Generación de direcciones URL en HTML</span><span class="sxs-lookup"><span data-stu-id="21cc5-850">Generating URLs in HTML</span></span>

<span data-ttu-id="21cc5-851">`IHtmlHelper` proporciona los métodos de `HtmlHelper``Html.BeginForm` y `Html.ActionLink` para generar elementos `<form>` y `<a>`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="21cc5-852">Estos métodos utilizan el método `Url.Action` para generar una dirección URL y aceptan argumentos similares.</span><span class="sxs-lookup"><span data-stu-id="21cc5-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="21cc5-853">Los métodos `Url.RouteUrl` complementarios de `HtmlHelper` son `Html.BeginRouteForm` y `Html.RouteLink`, cuya funcionalidad es similar.</span><span class="sxs-lookup"><span data-stu-id="21cc5-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="21cc5-854">Las TagHelper generan direcciones URL a través de la TagHelper `form` y la TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="21cc5-855">Ambos usan `IUrlHelper` para su implementación.</span><span class="sxs-lookup"><span data-stu-id="21cc5-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="21cc5-856">Consulte [Trabajar con formularios](../views/working-with-forms.md) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="21cc5-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="21cc5-857">Dentro de las vistas, `IUrlHelper` está disponible a través de la propiedad `Url` para una generación de direcciones URL ad hoc no cubierta por los pasos anteriores.</span><span class="sxs-lookup"><span data-stu-id="21cc5-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="21cc5-858">Generar direcciones URL en los resultados de acción</span><span class="sxs-lookup"><span data-stu-id="21cc5-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="21cc5-859">Los ejemplos anteriores han demostrado el uso de `IUrlHelper` en un controlador, aunque el uso más común de un controlador consiste en generar una dirección URL como parte de un resultado de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="21cc5-860">Las clases base `ControllerBase` y `Controller` proporcionan métodos de conveniencia para los resultados de acción que hacen referencia a otra acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="21cc5-861">Un uso típico es redirigir después de aceptar la entrada del usuario.</span><span class="sxs-lookup"><span data-stu-id="21cc5-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="21cc5-862">Los patrones de diseño Factory Method de resultados de acción siguen un patrón similar a los métodos en `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="21cc5-863">Caso especial para rutas convencionales dedicadas</span><span class="sxs-lookup"><span data-stu-id="21cc5-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="21cc5-864">El enrutamiento convencional puede usar un tipo especial de definición de ruta denominada *ruta convencional dedicada*.</span><span class="sxs-lookup"><span data-stu-id="21cc5-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="21cc5-865">En el ejemplo siguiente, la ruta llamada `blog` es una ruta convencional dedicada.</span><span class="sxs-lookup"><span data-stu-id="21cc5-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="21cc5-866">Con estas definiciones de ruta, `Url.Action("Index", "Home")` generará la ruta de dirección URL `/` con la ruta `default`. Pero, ¿por qué?</span><span class="sxs-lookup"><span data-stu-id="21cc5-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="21cc5-867">Se puede suponer que los valores de ruta `{ controller = Home, action = Index }` son suficientes para generar una dirección URL utilizando `blog`, con el resultado `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="21cc5-868">Las rutas convencionales dedicadas se basan en un comportamiento especial de los valores predeterminados que no tienen un parámetro de ruta correspondiente que impida que la ruta sea "demasiado expansiva" con la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="21cc5-869">En este caso, los valores predeterminados son `{ controller = Blog, action = Article }`, y ni `controller` ni `action` aparecen como un parámetro de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="21cc5-870">Cuando el enrutamiento realiza la generación de direcciones URL, los valores proporcionados deben coincidir con los valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="21cc5-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="21cc5-871">La generación de direcciones URL con `blog` producirá un error porque los valores `{ controller = Home, action = Index }` no coinciden con `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="21cc5-872">Después, el enrutamiento vuelve para probar `default`, operación que se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="21cc5-873">Áreas</span><span class="sxs-lookup"><span data-stu-id="21cc5-873">Areas</span></span>

<span data-ttu-id="21cc5-874">Las [áreas](areas.md) son una característica de MVC que se usa para organizar funciones relacionadas en un grupo como un espacio de nombres de enrutamiento independiente (para acciones de controlador) y una estructura de carpetas (para las vistas).</span><span class="sxs-lookup"><span data-stu-id="21cc5-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="21cc5-875">La utilización de áreas permite que una aplicación tenga varios controladores con el mismo nombre, siempre y cuando tengan *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="21cc5-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="21cc5-876">El uso de áreas crea una jerarquía para el enrutamiento mediante la adición de otro parámetro de ruta, `area`, a `controller` y `action`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="21cc5-877">En esta sección veremos la interacción entre el enrutamiento y las áreas. Consulte [Áreas](areas.md) para obtener más información sobre cómo se utilizan las áreas con las vistas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="21cc5-878">En el ejemplo siguiente se configura MVC para usar la ruta predeterminada convencional y una *ruta de área* para un área denominada `Blog`:</span><span class="sxs-lookup"><span data-stu-id="21cc5-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="21cc5-879">Cuando coincide con una ruta de dirección URL como `/Manage/Users/AddUser`, la primera ruta generará los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="21cc5-880">El valor de ruta `area` se genera con un valor predeterminado para `area`. De hecho, la ruta creada por `MapAreaRoute` es equivalente a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="21cc5-881">`MapAreaRoute` utiliza el nombre de área proporcionado, que en este caso es `Blog`, para crear una ruta con un valor predeterminado y una restricción para `area`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="21cc5-882">El valor predeterminado garantiza que la ruta siempre produce `{ area = Blog, ... }`; la restricción requiere el valor `{ area = Blog, ... }` para la generación de la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="21cc5-883">El enrutamiento convencional depende del orden.</span><span class="sxs-lookup"><span data-stu-id="21cc5-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="21cc5-884">En general, las rutas con áreas deben colocarse antes en la tabla de rutas, ya que son más específicas que las rutas sin un área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="21cc5-885">En el ejemplo anterior, los valores de ruta coincidirían con la acción siguiente:</span><span class="sxs-lookup"><span data-stu-id="21cc5-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="21cc5-886">`AreaAttribute` es lo que denota un controlador como parte de un área. Se dice que este controlador está en el área `Blog`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="21cc5-887">Los controladores sin un atributo `[Area]` no son miembros de ningún área y **no** coincidirán cuando el enrutamiento proporcione el valor de ruta `area`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="21cc5-888">En el ejemplo siguiente, solo el primer controlador enumerado puede coincidir con los valores de ruta `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="21cc5-889">En aras de la exhaustividad, aquí se muestra el espacio de nombres de cada controlador; en caso contrario, los controladores tendrían un conflicto de nomenclatura y generarían un error del compilador.</span><span class="sxs-lookup"><span data-stu-id="21cc5-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="21cc5-890">Los espacios de nombres de clase no tienen ningún efecto en el enrutamiento de MVC.</span><span class="sxs-lookup"><span data-stu-id="21cc5-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="21cc5-891">Los dos primeros controladores son miembros de las áreas y solo coinciden cuando el valor de ruta `area` proporciona su respectivo nombre de área.</span><span class="sxs-lookup"><span data-stu-id="21cc5-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="21cc5-892">El tercer controlador no es miembro de ningún área y solo puede coincidir cuando el enrutamiento no proporciona ningún valor para `area`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-893">En términos de búsqueda de coincidencias de *ningún valor*, la ausencia del valor `area` es igual que si el valor de `area` fuese null o una cadena vacía.</span><span class="sxs-lookup"><span data-stu-id="21cc5-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="21cc5-894">Al ejecutar una acción en un área, el valor de ruta para `area` estará disponible como un *valor de ambiente* para que el enrutamiento pueda usarlo en la generación de direcciones URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="21cc5-895">Esto significa que, de forma predeterminada, las áreas actúan de forma *adhesiva* para la generación de direcciones URL, tal como se muestra en el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="21cc5-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="21cc5-896">Descripción de IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="21cc5-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="21cc5-897">En esta sección se analiza con mayor profundidad el funcionamiento interno del marco y la elección por parte de MVC de la acción que se va a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="21cc5-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="21cc5-898">Una aplicación típica no necesitará un `IActionConstraint` personalizado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="21cc5-899">Es probable que ya haya usado `IActionConstraint` incluso si no está familiarizado con la interfaz.</span><span class="sxs-lookup"><span data-stu-id="21cc5-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="21cc5-900">El atributo `[HttpGet]` y los atributos `[Http-VERB]` similares implementan `IActionConstraint` con el fin de limitar la ejecución de un método de acción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="21cc5-901">Adoptando la ruta convencional predeterminada, la ruta de dirección URL `/Products/Edit` generaría los valores `{ controller = Products, action = Edit }`, que coincidirían con **ambas** acciones que se muestran aquí.</span><span class="sxs-lookup"><span data-stu-id="21cc5-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="21cc5-902">En terminología de `IActionConstraint`, diríamos que ambas acciones se consideran candidatas, puesto que las dos coinciden con los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="21cc5-903">Cuando `HttpGetAttribute` se ejecute, dirá que *Edit()* es una coincidencia para *GET*, pero no para cualquier otro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="21cc5-904">La acción `Edit(...)` no tiene ninguna restricción definida, por lo que coincidirá con cualquier verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="21cc5-905">Con `POST`, solamente `Edit(...)` coincide.</span><span class="sxs-lookup"><span data-stu-id="21cc5-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="21cc5-906">Pero con `GET` ambas acciones pueden coincidir. No obstante, una acción con `IActionConstraint` siempre se considera *mejor* que una acción sin dicha restricción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="21cc5-907">Por tanto, como `Edit()` tiene `[HttpGet]`, se considera más específica y se seleccionará si ambas acciones pueden coincidir.</span><span class="sxs-lookup"><span data-stu-id="21cc5-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="21cc5-908">Conceptualmente, `IActionConstraint` es una forma de *sobrecarga*, pero en lugar de sobrecargar métodos con el mismo nombre, la sobrecarga se produce entre acciones que coinciden con la misma dirección URL.</span><span class="sxs-lookup"><span data-stu-id="21cc5-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="21cc5-909">El enrutamiento mediante atributos también utiliza `IActionConstraint` y puede dar lugar a que acciones de diferentes controladores se consideren candidatas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="21cc5-910">Implementación de IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="21cc5-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="21cc5-911">La manera más sencilla de implementar `IActionConstraint` consiste en crear una clase derivada de `System.Attribute` y colocarla en las acciones y los controladores.</span><span class="sxs-lookup"><span data-stu-id="21cc5-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="21cc5-912">MVC detectará automáticamente cualquier `IActionConstraint` que se aplique como atributo.</span><span class="sxs-lookup"><span data-stu-id="21cc5-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="21cc5-913">El modelo de aplicaciones es quizá el enfoque más flexible para la aplicación de restricciones, puesto que permite metaprogramar cómo se aplican.</span><span class="sxs-lookup"><span data-stu-id="21cc5-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="21cc5-914">En el ejemplo siguiente, una restricción elige una acción basada en un código de *país* de los datos de ruta.</span><span class="sxs-lookup"><span data-stu-id="21cc5-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="21cc5-915">El [ejemplo completo se encuentra en GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="21cc5-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="21cc5-916">Usted es responsable de implementar el método `Accept` y elegir un valor para "Order" para que la restricción se ejecute.</span><span class="sxs-lookup"><span data-stu-id="21cc5-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="21cc5-917">En este caso, el método `Accept` devuelve `true` para denotar que la acción es una coincidencia cuando el valor de ruta `country` coincide.</span><span class="sxs-lookup"><span data-stu-id="21cc5-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="21cc5-918">Esto difiere de `RouteValueAttribute` en que permite volver a una acción sin atributos.</span><span class="sxs-lookup"><span data-stu-id="21cc5-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="21cc5-919">El ejemplo muestra que si se define una acción `en-US`, entonces un código de país como `fr-FR` recurriría a un controlador más genérico que no tenga `[CountrySpecific(...)]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="21cc5-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="21cc5-920">La propiedad `Order` decide de qué *fase* forma parte la restricción.</span><span class="sxs-lookup"><span data-stu-id="21cc5-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="21cc5-921">Restricciones de acciones que se ejecutan en grupos basados en la `Order`.</span><span class="sxs-lookup"><span data-stu-id="21cc5-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="21cc5-922">Por ejemplo, todos los atributos del método HTTP proporcionados por el marco utilizan el mismo valor `Order` para que se ejecuten en la misma fase.</span><span class="sxs-lookup"><span data-stu-id="21cc5-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="21cc5-923">Puede tener las fases que sean necesarias para implementar las directivas deseadas.</span><span class="sxs-lookup"><span data-stu-id="21cc5-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="21cc5-924">Para decidir el valor de `Order`, piense si la restricción se debería o no aplicar antes que los métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="21cc5-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="21cc5-925">Los números más bajos se ejecutan primero.</span><span class="sxs-lookup"><span data-stu-id="21cc5-925">Lower numbers run first.</span></span>

::: moniker-end
