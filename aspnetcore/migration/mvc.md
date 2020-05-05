---
title: Migración de ASP.NET MVC a ASP.NET Core MVC
author: ardalis
description: Obtenga información sobre cómo empezar a migrar un proyecto de ASP.NET MVC a ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777051"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="c3e9f-103">Migración de ASP.NET MVC a ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="c3e9f-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="c3e9f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)y [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="c3e9f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="c3e9f-105">En este artículo se muestra cómo empezar a migrar un proyecto de ASP.NET MVC a [ASP.net Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="c3e9f-106">En el proceso, se resaltan muchas de las cosas que han cambiado de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="c3e9f-107">La migración de ASP.NET MVC es un proceso de varios pasos y en este artículo se trata la configuración inicial, los controladores y las vistas básicos, el contenido estático y las dependencias del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="c3e9f-108">Los artículos adicionales cubren la migración de la configuración y el código de identidad que se encuentran en muchos proyectos de MVC de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="c3e9f-109">Es posible que los números de versión de los ejemplos no estén actualizados.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="c3e9f-110">Es posible que tenga que actualizar los proyectos en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="c3e9f-111">Crear el proyecto de MVC ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="c3e9f-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="c3e9f-112">Para demostrar la actualización, comenzaremos por crear una aplicación de ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="c3e9f-113">Créelo con el nombre *WebApp1* para que el espacio de nombres coincida con el ASP.net Core proyecto que creamos en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Cuadro de diálogo Nuevo proyecto de Visual Studio](mvc/_static/new-project.png)

![Cuadro de diálogo Nueva aplicación web: plantilla de proyecto de MVC seleccionada en el panel de plantillas de ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="c3e9f-116">*Opcional:* Cambie el nombre de la solución de *WebApp1* a *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="c3e9f-117">Visual Studio muestra el nuevo nombre de la solución (*Mvc5*), lo que facilita la información de este proyecto desde el proyecto siguiente.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="c3e9f-118">Crear el proyecto de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3e9f-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="c3e9f-119">Cree una nueva aplicación Web *vacía* de ASP.net Core con el mismo nombre que el proyecto anterior (*WebApp1*) para que coincidan los espacios de nombres de los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="c3e9f-120">Tener el mismo espacio de nombres facilita la copia de código entre los dos proyectos.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="c3e9f-121">Tendrá que crear este proyecto en un directorio diferente al del proyecto anterior para usar el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Cuadro de diálogo Nuevo proyecto](mvc/_static/new_core.png)

![Cuadro de diálogo Nueva aplicación Web de ASP.NET: plantilla de proyecto vacía seleccionada en el panel de plantillas de ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="c3e9f-124">*Opcional:* Cree una nueva aplicación de ASP.NET Core mediante la plantilla de proyecto de *aplicación web* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="c3e9f-125">Asigne al proyecto el nombre *WebApp1*y seleccione una opción de autenticación de **cuentas de usuario individuales**.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="c3e9f-126">Cambie el nombre de esta aplicación a *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="c3e9f-127">Al crear este proyecto, se ahorra tiempo en la conversión.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="c3e9f-128">Puede examinar el código generado por la plantilla para ver el resultado final o copiar el código en el proyecto de conversión.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="c3e9f-129">También resulta útil cuando se queda bloqueado en un paso de conversión para compararlo con el proyecto generado por una plantilla.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="c3e9f-130">Configurar el sitio para usar MVC</span><span class="sxs-lookup"><span data-stu-id="c3e9f-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="c3e9f-131">Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="c3e9f-132">Este paquete contiene los paquetes que usan normalmente las aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="c3e9f-133">Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="c3e9f-134">Cuando el destino es .NET Core, se hace referencia al [metapaquete Microsoft. AspNetCore. All](xref:fundamentals/metapackage) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="c3e9f-135">Este paquete contiene paquetes usados comúnmente por aplicaciones MVC.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="c3e9f-136">Si el destino es .NET Framework, las referencias del paquete deben aparecer de forma individual en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="c3e9f-137">Cuando el destino es .NET Core o .NET Framework, los paquetes usados comúnmente por las aplicaciones MVC se enumeran individualmente en el archivo del proyecto.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="c3e9f-138">`Microsoft.AspNetCore.Mvc`es el marco de MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="c3e9f-139">`Microsoft.AspNetCore.StaticFiles`es el controlador de archivos estático.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="c3e9f-140">El tiempo de ejecución de ASP.NET Core es modular y debe participar explícitamente para servir archivos estáticos (vea [archivos estáticos](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="c3e9f-141">Abra el archivo *Startup.CS* y cambie el código para que coincida con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="c3e9f-142">El `UseStaticFiles` método de extensión agrega el controlador de archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="c3e9f-143">Como se mencionó anteriormente, el tiempo de ejecución de ASP.NET es modular y debe participar explícitamente para servir archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="c3e9f-144">El `UseMvc` método de extensión agrega enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="c3e9f-145">Para obtener más información, consulte Inicio y [enrutamiento](xref:fundamentals/routing)de la [aplicación](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="c3e9f-146">Agregar un controlador y una vista</span><span class="sxs-lookup"><span data-stu-id="c3e9f-146">Add a controller and view</span></span>

<span data-ttu-id="c3e9f-147">En esta sección, agregará un controlador y una vista mínimos para que sirvan como marcadores de posición para el controlador ASP.NET MVC y las vistas que va a migrar en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="c3e9f-148">Agregue una carpeta *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="c3e9f-149">Agregue una **clase de controlador** denominada *HomeController.CS* a la carpeta *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="c3e9f-151">Agregue una carpeta *views* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="c3e9f-152">Agregue una carpeta *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="c3e9f-153">Agregue una \*\* Razor vista\*\* denominada *index. cshtml* a la carpeta *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Cuadro de diálogo Agregar nuevo elemento](mvc/_static/view.png)

<span data-ttu-id="c3e9f-155">La estructura del proyecto se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-155">The project structure is shown below:</span></span>

![Explorador de soluciones que muestran archivos y carpetas de WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="c3e9f-157">Reemplace el contenido del archivo *views/home/index. cshtml* con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="c3e9f-158">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-158">Run the app.</span></span>

![Aplicación web abierta en Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="c3e9f-160">Consulte [Controladores](xref:mvc/controllers/actions) y [vistas](xref:mvc/views/overview) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="c3e9f-161">Ahora que tenemos un proyecto de ASP.NET Core de trabajo mínimo, podemos empezar a migrar la funcionalidad del proyecto ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="c3e9f-162">Necesitamos trasladar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-162">We need to move the following:</span></span>

* <span data-ttu-id="c3e9f-163">contenido del lado cliente (CSS, fuentes y scripts)</span><span class="sxs-lookup"><span data-stu-id="c3e9f-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="c3e9f-164">controllers</span><span class="sxs-lookup"><span data-stu-id="c3e9f-164">controllers</span></span>

* <span data-ttu-id="c3e9f-165">views</span><span class="sxs-lookup"><span data-stu-id="c3e9f-165">views</span></span>

* <span data-ttu-id="c3e9f-166">modelos</span><span class="sxs-lookup"><span data-stu-id="c3e9f-166">models</span></span>

* <span data-ttu-id="c3e9f-167">Unión</span><span class="sxs-lookup"><span data-stu-id="c3e9f-167">bundling</span></span>

* <span data-ttu-id="c3e9f-168">filters</span><span class="sxs-lookup"><span data-stu-id="c3e9f-168">filters</span></span>

* <span data-ttu-id="c3e9f-169">Iniciar y cerrar sesión Identity (esto se hace en el siguiente tutorial).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="c3e9f-170">Controladores y vistas</span><span class="sxs-lookup"><span data-stu-id="c3e9f-170">Controllers and views</span></span>

* <span data-ttu-id="c3e9f-171">Copie cada uno de los métodos de MVC `HomeController` de ASP.net al nuevo `HomeController`.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="c3e9f-172">Tenga en cuenta que en ASP.NET MVC, el tipo de valor devuelto del método de acción del controlador de la plantilla integrada es [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx). en ASP.NET Core MVC, los métodos de acción `IActionResult` devuelven en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="c3e9f-173">`ActionResult`implementa `IActionResult`, por lo que no es necesario cambiar el tipo de valor devuelto de los métodos de acción.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="c3e9f-174">Copie los archivos de vista *About. cshtml*, *Contact. cshtml*e *index. cshtml* Razor del proyecto ASP.NET MVC en el proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="c3e9f-175">Ejecute la aplicación ASP.NET Core y pruebe cada método.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="c3e9f-176">Todavía no se han migrado el archivo de diseño o los estilos, por lo que las vistas representadas solo contienen el contenido de los archivos de vista.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="c3e9f-177">No tendrá los vínculos generados por el archivo de `About` diseño `Contact` para las vistas y, por lo que tendrá que invocarlos desde el explorador (Reemplace **4492** por el número de Puerto usado en el proyecto).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Página de contacto](mvc/_static/contact-page.png)

<span data-ttu-id="c3e9f-179">Tenga en cuenta la falta de estilo y elementos de menú.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="c3e9f-180">Esto lo corregiremos en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="c3e9f-181">Contenido estático</span><span class="sxs-lookup"><span data-stu-id="c3e9f-181">Static content</span></span>

<span data-ttu-id="c3e9f-182">En versiones anteriores de ASP.NET MVC, el contenido estático se hospedaba en la raíz del proyecto web y se combinaba con los archivos del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="c3e9f-183">En ASP.NET Core, el contenido estático se hospeda en la carpeta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="c3e9f-184">Querrá copiar el contenido estático de la aplicación ASP.NET MVC antigua en la carpeta *wwwroot* del proyecto de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="c3e9f-185">En esta conversión de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-185">In this sample conversion:</span></span>

* <span data-ttu-id="c3e9f-186">Copie el archivo *favicon. ico* del proyecto de MVC anterior en la carpeta *wwwroot* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="c3e9f-187">El antiguo proyecto ASP.NET MVC usa [bootstrap](https://getbootstrap.com/) para su estilo y almacena los archivos de arranque en las carpetas *contenido* y *scripts* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="c3e9f-188">La plantilla, que generó el proyecto ASP.NET MVC anterior, hace referencia a bootstrap en el archivo de diseño (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="c3e9f-189">Puede copiar los archivos *bootstrap. js* y *bootstrap. css* del proyecto ASP.NET MVC en la carpeta *wwwroot* del nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="c3e9f-190">En su lugar, agregaremos compatibilidad con bootstrap (y otras bibliotecas del lado cliente) mediante redes CDN en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="c3e9f-191">Migrar el archivo de diseño</span><span class="sxs-lookup"><span data-stu-id="c3e9f-191">Migrate the layout file</span></span>

* <span data-ttu-id="c3e9f-192">Copie el archivo *_ViewStart. cshtml* de la carpeta *views* del proyecto de ASP.NET MVC antigua en la carpeta *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="c3e9f-193">El archivo *_ViewStart. cshtml* no ha cambiado en ASP.net Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="c3e9f-194">Cree una carpeta *views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="c3e9f-195">*Opcional:* Copie *_ViewImports. cshtml* de la carpeta *views* del proyecto de *FullAspNetCore* MVC en la carpeta *views* del proyecto ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="c3e9f-196">Quite cualquier declaración de espacio de nombres del archivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c3e9f-197">El archivo *_ViewImports. cshtml* proporciona espacios de nombres para todos los archivos de vista y ofrece [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="c3e9f-198">Las aplicaciones auxiliares de etiquetas se usan en el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="c3e9f-199">El archivo *_ViewImports. cshtml* es nuevo para ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="c3e9f-200">Copie el archivo *_Layout. cshtml* de la carpeta de *vistas/compartidas* del proyecto de ASP.NET MVC anterior en la carpeta *views/Shared* del proyecto de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="c3e9f-201">Abra *_Layout archivo. cshtml* y realice los cambios siguientes (el código completo se muestra a continuación):</span><span class="sxs-lookup"><span data-stu-id="c3e9f-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="c3e9f-202">Reemplace `@Styles.Render("~/Content/css")` por un `<link>` elemento para cargar *bootstrap. CSS* (consulte a continuación).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="c3e9f-203">Quite `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="c3e9f-204">Comente la `@Html.Partial("_LoginPartial")` línea (rodee la línea con `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="c3e9f-205">Para obtener más información [, vea migrar Identity la autenticación y ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="c3e9f-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="c3e9f-206">Reemplazar `@Scripts.Render("~/bundles/jquery")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="c3e9f-207">Reemplazar `@Scripts.Render("~/bundles/bootstrap")` por un `<script>` elemento (vea más abajo).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="c3e9f-208">El marcado de reemplazo para la inclusión de CSS de bootstrap:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="c3e9f-209">El marcado de reemplazo para jQuery y bootstrap JavaScript incluirá:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="c3e9f-210">El archivo *_Layout. cshtml* actualizado se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="c3e9f-211">Vea el sitio en el explorador.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-211">View the site in the browser.</span></span> <span data-ttu-id="c3e9f-212">Ahora debería cargarse correctamente, con los estilos esperados en su lugar.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="c3e9f-213">*Opcional:* Es posible que desee intentar usar el nuevo archivo de diseño.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="c3e9f-214">Para este proyecto, puede copiar el archivo de diseño del proyecto *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="c3e9f-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="c3e9f-215">El nuevo archivo de diseño utiliza [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) y otras mejoras.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="c3e9f-216">Configuración de la unión y la minimización</span><span class="sxs-lookup"><span data-stu-id="c3e9f-216">Configure bundling and minification</span></span>

<span data-ttu-id="c3e9f-217">Para obtener información acerca de cómo configurar la agrupación y la minificación, consulte [agrupación y minificación](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="c3e9f-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="c3e9f-218">Solucionar errores HTTP 500</span><span class="sxs-lookup"><span data-stu-id="c3e9f-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="c3e9f-219">Hay muchos problemas que pueden provocar un mensaje de error HTTP 500 que no contiene información sobre el origen del problema.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="c3e9f-220">Por ejemplo, si el archivo *views/_ViewImports. cshtml* contiene un espacio de nombres que no existe en el proyecto, obtendrá un error http 500.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="c3e9f-221">De forma predeterminada, en ASP.NET Core aplicaciones `UseDeveloperExceptionPage` , la extensión se agrega `IApplicationBuilder` a y se ejecuta cuando la configuración es *desarrollo*.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="c3e9f-222">Esto se detalla en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="c3e9f-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="c3e9f-223">ASP.NET Core convierte las excepciones no controladas en una aplicación web en respuestas de error HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="c3e9f-224">Normalmente, los detalles del error no se incluyen en estas respuestas para evitar la divulgación de información potencialmente confidencial sobre el servidor.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="c3e9f-225">Consulte **uso de la página de excepción para desarrolladores** en [controlar errores](../fundamentals/error-handling.md) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="c3e9f-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3e9f-226">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="c3e9f-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
