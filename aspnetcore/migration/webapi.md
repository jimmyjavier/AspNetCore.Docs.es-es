---
title: Migrar de ASP.NET Web API a ASP.NET Core
author: ardalis
description: Obtenga información sobre cómo migrar una implementación de Web API desde la API Web de ASP.NET 4. x ASP.NET Core a MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766594"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="43820-103">Migrar de ASP.NET Web API a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43820-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="43820-104">Por [Scott Addie](https://twitter.com/scott_addie) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="43820-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="43820-105">Una API Web ASP.NET 4. x es un servicio HTTP que llega a una amplia gama de clientes, incluidos exploradores y dispositivos móviles.</span><span class="sxs-lookup"><span data-stu-id="43820-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="43820-106">ASP.NET Core unifica los modelos de aplicaciones MVC y Web API de ASP.NET 4. x en un modelo de programación más sencillo conocido como ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="43820-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="43820-107">En este artículo se muestran los pasos necesarios para migrar desde la API Web de ASP.NET 4. x ASP.NET Core a MVC.</span><span class="sxs-lookup"><span data-stu-id="43820-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="43820-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43820-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="43820-109">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="43820-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="43820-110">Revisar el proyecto de Web API de ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="43820-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="43820-111">Como punto de partida, en este artículo se usa el proyecto *ProductsApp* creado en [Introducción con ASP.net web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="43820-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="43820-112">En ese proyecto, se configura un proyecto de API Web ASP.NET 4. x simple como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="43820-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="43820-113">En *global.asax.CS*, se realiza una llamada a `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="43820-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="43820-114">La `WebApiConfig` clase se encuentra en la carpeta *App_Start* y tiene un método `Register` estático:</span><span class="sxs-lookup"><span data-stu-id="43820-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="43820-115">Esta clase configura el [enrutamiento de atributos](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), aunque en realidad no se usa en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="43820-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="43820-116">También configura la tabla de enrutamiento, que se usa en ASP.NET Web API.</span><span class="sxs-lookup"><span data-stu-id="43820-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="43820-117">En este caso, la API Web de ASP.NET 4. x espera que las direcciones URL `/api/{controller}/{id}`coincidan `{id}` con el formato, siendo opcionales.</span><span class="sxs-lookup"><span data-stu-id="43820-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="43820-118">El proyecto *ProductsApp* incluye un controlador.</span><span class="sxs-lookup"><span data-stu-id="43820-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="43820-119">El controlador hereda de `ApiController` y contiene dos acciones:</span><span class="sxs-lookup"><span data-stu-id="43820-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="43820-120">El `Product` modelo usado por `ProductsController` es una clase simple:</span><span class="sxs-lookup"><span data-stu-id="43820-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="43820-121">En las secciones siguientes se muestra cómo migrar el proyecto de Web API a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="43820-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="43820-122">Crear proyecto de destino</span><span class="sxs-lookup"><span data-stu-id="43820-122">Create destination project</span></span>

<span data-ttu-id="43820-123">Siga los pasos que se describen a continuación en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="43820-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="43820-124">Vaya a **archivo** > **nuevo** > **proyecto** > **otros tipos** > de proyecto**soluciones de Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="43820-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="43820-125">Seleccione **solución en blanco**y asigne el nombre *WebAPIMigration*a la solución.</span><span class="sxs-lookup"><span data-stu-id="43820-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="43820-126">Haga clic en el botón **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="43820-126">Click the **OK** button.</span></span>
* <span data-ttu-id="43820-127">Agregue el proyecto *ProductsApp* existente a la solución.</span><span class="sxs-lookup"><span data-stu-id="43820-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="43820-128">Agregue un nuevo proyecto de **aplicación Web de ASP.net Core** a la solución.</span><span class="sxs-lookup"><span data-stu-id="43820-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="43820-129">Seleccione la plataforma de destino de **.net Core** en la lista desplegable y seleccione la plantilla de proyecto de **API** .</span><span class="sxs-lookup"><span data-stu-id="43820-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="43820-130">Asigne al proyecto el nombre *ProductsCore*y haga clic en el botón **Aceptar** .</span><span class="sxs-lookup"><span data-stu-id="43820-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="43820-131">La solución ahora contiene dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="43820-131">The solution now contains two projects.</span></span> <span data-ttu-id="43820-132">En las secciones siguientes se explica cómo migrar el contenido del proyecto *ProductsApp* al proyecto *ProductsCore* .</span><span class="sxs-lookup"><span data-stu-id="43820-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="43820-133">Migración de la configuración</span><span class="sxs-lookup"><span data-stu-id="43820-133">Migrate configuration</span></span>

<span data-ttu-id="43820-134">ASP.NET Core no utiliza la carpeta *App_Start* o el archivo *global. asax* , y el archivo *Web. config* se agrega en el momento de la publicación.</span><span class="sxs-lookup"><span data-stu-id="43820-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="43820-135">*Startup.CS* es el sustituto de *global. asax* y se encuentra en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="43820-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="43820-136">La `Startup` clase controla todas las tareas de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="43820-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="43820-137">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="43820-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="43820-138">En ASP.NET Core MVC, el enrutamiento de atributos se incluye de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> forma predeterminada cuando `Startup.Configure`se llama a en.</span><span class="sxs-lookup"><span data-stu-id="43820-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="43820-139">La siguiente `UseMvc` llamada reemplaza el archivo *App_Start/Webapiconfig.CS* del proyecto *ProductsApp* :</span><span class="sxs-lookup"><span data-stu-id="43820-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="43820-140">Migración de modelos y controladores</span><span class="sxs-lookup"><span data-stu-id="43820-140">Migrate models and controllers</span></span>

<span data-ttu-id="43820-141">Copie el controlador del proyecto de *ProductApp* y el modelo que usa.</span><span class="sxs-lookup"><span data-stu-id="43820-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="43820-142">Siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="43820-142">Follow these steps:</span></span>

1. <span data-ttu-id="43820-143">Copie *Controllers/ProductsController. CS* del proyecto original al nuevo.</span><span class="sxs-lookup"><span data-stu-id="43820-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="43820-144">Copie toda la carpeta *Models* del proyecto original al nuevo.</span><span class="sxs-lookup"><span data-stu-id="43820-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="43820-145">Cambie los espacios de nombres de los archivos copiados para que coincidan con el nuevo nombre del proyecto (*ProductsCore*).</span><span class="sxs-lookup"><span data-stu-id="43820-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="43820-146">Ajuste también `using ProductsApp.Models;` la instrucción en *ProductsController.CS* .</span><span class="sxs-lookup"><span data-stu-id="43820-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="43820-147">En este punto, la creación de la aplicación produce una serie de errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="43820-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="43820-148">Los errores se producen porque los componentes siguientes no existen en ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="43820-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="43820-149">Clase `ApiController`</span><span class="sxs-lookup"><span data-stu-id="43820-149">`ApiController` class</span></span>
* <span data-ttu-id="43820-150">Espacio de nombres `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="43820-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="43820-151">`IHttpActionResult` (interfaz)</span><span class="sxs-lookup"><span data-stu-id="43820-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="43820-152">Corrija los errores de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="43820-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="43820-153">Cambio de `ApiController` a <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="43820-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="43820-154">Agregue `using Microsoft.AspNetCore.Mvc;` para resolver la `ControllerBase` referencia.</span><span class="sxs-lookup"><span data-stu-id="43820-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="43820-155">Elimine `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="43820-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="43820-156">Cambie el `GetProduct` tipo de valor devuelto `IHttpActionResult` de `ActionResult<Product>`la acción de a.</span><span class="sxs-lookup"><span data-stu-id="43820-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="43820-157">Simplifique la `GetProduct` instrucción de `return` la acción para lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="43820-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="43820-158">Configuración del enrutamiento</span><span class="sxs-lookup"><span data-stu-id="43820-158">Configure routing</span></span>

<span data-ttu-id="43820-159">Configure el enrutamiento de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="43820-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="43820-160">Marque la `ProductsController` clase con los siguientes atributos:</span><span class="sxs-lookup"><span data-stu-id="43820-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="43820-161">El atributo [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) anterior configura el patrón de enrutamiento de atributo del controlador.</span><span class="sxs-lookup"><span data-stu-id="43820-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="43820-162">El [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atributo hace que el enrutamiento de atributos sea un requisito para todas las acciones de este controlador.</span><span class="sxs-lookup"><span data-stu-id="43820-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="43820-163">El enrutamiento de atributos admite tokens, `[controller]` como `[action]`y.</span><span class="sxs-lookup"><span data-stu-id="43820-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="43820-164">En tiempo de ejecución, cada token se reemplaza por el nombre del controlador o la acción, respectivamente, a la que se ha aplicado el atributo.</span><span class="sxs-lookup"><span data-stu-id="43820-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="43820-165">Los tokens reducen el número de cadenas mágicas del proyecto.</span><span class="sxs-lookup"><span data-stu-id="43820-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="43820-166">Los tokens también garantizan que las rutas permanezcan sincronizadas con los controladores y acciones correspondientes cuando se apliquen las refactorizaciones de cambio de nombre automáticas.</span><span class="sxs-lookup"><span data-stu-id="43820-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="43820-167">Establezca el modo de compatibilidad del proyecto en ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="43820-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="43820-168">El cambio anterior:</span><span class="sxs-lookup"><span data-stu-id="43820-168">The preceding change:</span></span>

    * <span data-ttu-id="43820-169">Se requiere para usar el `[ApiController]` atributo en el nivel de controlador.</span><span class="sxs-lookup"><span data-stu-id="43820-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="43820-170">Opta por los posibles saltos de los comportamientos introducidos en ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="43820-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="43820-171">Habilite las solicitudes HTTP GET para `ProductController` las acciones:</span><span class="sxs-lookup"><span data-stu-id="43820-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="43820-172">Aplique el [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atributo a la `GetAllProducts` acción.</span><span class="sxs-lookup"><span data-stu-id="43820-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="43820-173">Aplique el `[HttpGet("{id}")]` atributo a la `GetProduct` acción.</span><span class="sxs-lookup"><span data-stu-id="43820-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="43820-174">Después de los cambios anteriores y la eliminación de instrucciones `using` no utilizadas, el archivo *ProductsController.CS* tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="43820-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="43820-175">Ejecute el proyecto migrado y vaya a `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="43820-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="43820-176">Aparece una lista completa de tres productos.</span><span class="sxs-lookup"><span data-stu-id="43820-176">A full list of three products appears.</span></span> <span data-ttu-id="43820-177">Vaya a `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="43820-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="43820-178">Aparece el primer producto.</span><span class="sxs-lookup"><span data-stu-id="43820-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="43820-179">Corrección de compatibilidad</span><span class="sxs-lookup"><span data-stu-id="43820-179">Compatibility shim</span></span>

<span data-ttu-id="43820-180">La biblioteca [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) proporciona una corrección de compatibilidad para migrar los proyectos de API Web de ASP.net 4. x a ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="43820-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="43820-181">La corrección de compatibilidad amplía ASP.NET Core para admitir una serie de convenciones de la API Web de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="43820-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="43820-182">El ejemplo que se ha migrado anteriormente en este documento es lo suficientemente básico como para que la corrección de compatibilidad no fuera necesaria.</span><span class="sxs-lookup"><span data-stu-id="43820-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="43820-183">En el caso de los proyectos de mayor tamaño, el uso de la corrección de compatibilidad puede ser útil para el puente temporal entre ASP.NET Core y ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="43820-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="43820-184">La corrección de compatibilidad de la API Web está pensada para usarse como medida temporal para admitir la migración de proyectos de API Web de ASP.NET 4. x grandes a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43820-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="43820-185">Con el tiempo, los proyectos deben actualizarse para usar patrones de ASP.NET Core en lugar de basarse en la corrección de compatibilidad.</span><span class="sxs-lookup"><span data-stu-id="43820-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="43820-186">Las características de compatibilidad `Microsoft.AspNetCore.Mvc.WebApiCompatShim` incluidas en incluyen:</span><span class="sxs-lookup"><span data-stu-id="43820-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="43820-187">Agrega un `ApiController` tipo para que no sea necesario actualizar los tipos base de los controladores.</span><span class="sxs-lookup"><span data-stu-id="43820-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="43820-188">Habilita el enlace de modelos de estilo de API Web.</span><span class="sxs-lookup"><span data-stu-id="43820-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="43820-189">ASP.NET Core enlace de modelos de MVC funciona de forma predeterminada con el de ASP.NET 4. x MVC 5.</span><span class="sxs-lookup"><span data-stu-id="43820-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="43820-190">La corrección de compatibilidad cambia el enlace de modelos para que sea más similar a las convenciones de enlace de modelos de la API Web 2 de ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="43820-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="43820-191">Por ejemplo, los tipos complejos se enlazan automáticamente desde el cuerpo de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="43820-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="43820-192">Extiende el enlace de modelos para que las acciones de controlador puedan `HttpRequestMessage`Tomar parámetros de tipo.</span><span class="sxs-lookup"><span data-stu-id="43820-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="43820-193">Agrega formateadores de mensajes que permiten que las acciones devuelvan resultados de tipo `HttpResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="43820-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="43820-194">Agrega métodos de respuesta adicionales que pueden usar las acciones de Web API 2 para servir respuestas:</span><span class="sxs-lookup"><span data-stu-id="43820-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="43820-195">`HttpResponseMessage`productores</span><span class="sxs-lookup"><span data-stu-id="43820-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="43820-196">Métodos del resultado de la acción:</span><span class="sxs-lookup"><span data-stu-id="43820-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="43820-197">Agrega una instancia de `IContentNegotiator` al contenedor de inserción de dependencias (di) de la aplicación y pone a disposición los tipos relacionados con la negociación de contenido de [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="43820-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="43820-198">Entre los ejemplos de estos `DefaultContentNegotiator` tipos `MediaTypeFormatter`se incluyen y.</span><span class="sxs-lookup"><span data-stu-id="43820-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="43820-199">Para usar la corrección de compatibilidad:</span><span class="sxs-lookup"><span data-stu-id="43820-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="43820-200">Instale el paquete NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="43820-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="43820-201">Registre los servicios de la corrección de compatibilidad con el contenedor de DI de `services.AddMvc().AddWebApiConventions()` la `Startup.ConfigureServices`aplicación mediante una llamada a en.</span><span class="sxs-lookup"><span data-stu-id="43820-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="43820-202">Defina rutas específicas de la API Web `MapWebApiRoute` mediante en `IRouteBuilder` en la `IApplicationBuilder.UseMvc` llamada de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="43820-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43820-203">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="43820-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
