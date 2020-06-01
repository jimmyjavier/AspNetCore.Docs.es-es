---
<span data-ttu-id="c9189-101">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-103">'Blazor'</span></span>
- <span data-ttu-id="c9189-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-104">'Identity'</span></span>
- <span data-ttu-id="c9189-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-106">'Razor'</span></span>
- <span data-ttu-id="c9189-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c9189-108">Integración de componentes de Razor de ASP.NET Core en aplicaciones de Razor Pages y MVC</span><span class="sxs-lookup"><span data-stu-id="c9189-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="c9189-109">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c9189-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c9189-110">Los componentes Razor se pueden integrar en aplicaciones de Razor Pages y MVC.</span><span class="sxs-lookup"><span data-stu-id="c9189-110">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="c9189-111">Cuando se representa la página o la vista, los componentes se pueden representar previamente al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="c9189-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="c9189-112">Después de [preparar la aplicación](#prepare-the-app), use las instrucciones que aparecen en las secciones siguientes en función de los requisitos de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c9189-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="c9189-113">Componentes enrutables: para los componentes que se pueden enrutar directamente desde las solicitudes del usuario.</span><span class="sxs-lookup"><span data-stu-id="c9189-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="c9189-114">Siga estas instrucciones cuando los visitantes puedan hacer una solicitud HTTP en el explorador para un componente con una directiva [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="c9189-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="c9189-115">[Uso de componentes enrutables en una aplicación Razor Pages](#use-routable-components-in-a-razor-pages-app).</span><span class="sxs-lookup"><span data-stu-id="c9189-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * <span data-ttu-id="c9189-116">[Uso de componentes enrutables en una aplicación MVC](#use-routable-components-in-an-mvc-app).</span><span class="sxs-lookup"><span data-stu-id="c9189-116">[Use routable components in an MVC app](#use-routable-components-in-an-mvc-app)</span></span>
* <span data-ttu-id="c9189-117">[Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view): para los componentes que no se pueden enrutar directamente desde las solicitudes del usuario.</span><span class="sxs-lookup"><span data-stu-id="c9189-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="c9189-118">Siga estas instrucciones cuando la aplicación inserte componentes en páginas y vistas existentes con el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="c9189-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="c9189-119">Preparación de la aplicación</span><span class="sxs-lookup"><span data-stu-id="c9189-119">Prepare the app</span></span>

<span data-ttu-id="c9189-120">Una aplicación Razor Pages o MVC existente puede integrar componentes Razor en páginas y vistas:</span><span class="sxs-lookup"><span data-stu-id="c9189-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="c9189-121">En el archivo de diseño de la aplicación ( *_Layout.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="c9189-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="c9189-122">Agregue la etiqueta `<base>` siguiente al elemento `<head>`:</span><span class="sxs-lookup"><span data-stu-id="c9189-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="c9189-123">El valor `href` (la *ruta de acceso base de la aplicación*) del ejemplo anterior da por hecho que la aplicación reside en la ruta de acceso URL raíz (`/`).</span><span class="sxs-lookup"><span data-stu-id="c9189-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="c9189-124">Si la aplicación es una subaplicación, siga las instrucciones de la sección *Ruta de acceso base de la aplicación* del artículo <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="c9189-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="c9189-125">El archivo *_Layout.cshtml* se encuentra en la carpeta *Pages/Shared* en una aplicación Razor Pages o en la carpeta *Views/Shared* en una aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="c9189-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="c9189-126">Agregue una etiqueta `<script>` para el script *blazor.server.js* inmediatamente antes de la etiqueta `</body>` de cierre:</span><span class="sxs-lookup"><span data-stu-id="c9189-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="c9189-127">El marco agrega el script *blazor.server.js* a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c9189-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="c9189-128">No es necesario agregar manualmente el script a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c9189-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="c9189-129">Agregue un archivo *_Imports.razor* a la carpeta raíz del proyecto con el contenido siguiente (cambie el último espacio de nombres, `MyAppNamespace`, al espacio de nombres de la aplicación):</span><span class="sxs-lookup"><span data-stu-id="c9189-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="c9189-130">En `Startup.ConfigureServices`, registre el servicio Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="c9189-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="c9189-131">En `Startup.Configure`, agregue el punto de conexión de Blazor Hub a `app.UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="c9189-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="c9189-132">Integre los componentes en cualquier página o vista.</span><span class="sxs-lookup"><span data-stu-id="c9189-132">Integrate components into any page or view.</span></span> <span data-ttu-id="c9189-133">Para obtener más información, vea la sección [Representación de componentes a partir de una página o vista](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="c9189-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="c9189-134">Uso de componentes enrutables en una aplicación Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c9189-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="c9189-135">*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="c9189-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="c9189-136">Para admitir componentes Razor enrutables en aplicaciones Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="c9189-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="c9189-137">Siga las instrucciones que aparecen en la sección [Preparación de la aplicación](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="c9189-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="c9189-138">Agregue un archivo *App.razor* a la raíz del proyecto con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9189-138">Add an *App.razor* file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="c9189-139">Agregue un archivo *_Host.cshtml* a la carpeta *Pages* con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9189-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="c9189-140">Los componentes usan el archivo compartido *_Layout.cshtml* para su diseño.</span><span class="sxs-lookup"><span data-stu-id="c9189-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="c9189-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:</span><span class="sxs-lookup"><span data-stu-id="c9189-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="c9189-142">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="c9189-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="c9189-143">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="c9189-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="c9189-144">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="c9189-144">Render Mode</span></span> | <span data-ttu-id="c9189-145">Descripción</span><span class="sxs-lookup"><span data-stu-id="c9189-145">Description</span></span> |
   | ---
<span data-ttu-id="c9189-146">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-148">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-148">'Blazor'</span></span>
- <span data-ttu-id="c9189-149">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-149">'Identity'</span></span>
- <span data-ttu-id="c9189-150">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-151">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-151">'Razor'</span></span>
- <span data-ttu-id="c9189-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-153">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-155">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-155">'Blazor'</span></span>
- <span data-ttu-id="c9189-156">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-156">'Identity'</span></span>
- <span data-ttu-id="c9189-157">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-158">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-158">'Razor'</span></span>
- <span data-ttu-id="c9189-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-160">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-162">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-162">'Blazor'</span></span>
- <span data-ttu-id="c9189-163">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-163">'Identity'</span></span>
- <span data-ttu-id="c9189-164">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-165">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-165">'Razor'</span></span>
- <span data-ttu-id="c9189-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-166">'SignalR' uid:</span></span> 

<span data-ttu-id="c9189-167">------ | --- title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-169">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-169">'Blazor'</span></span>
- <span data-ttu-id="c9189-170">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-170">'Identity'</span></span>
- <span data-ttu-id="c9189-171">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-172">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-172">'Razor'</span></span>
- <span data-ttu-id="c9189-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-174">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-176">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-176">'Blazor'</span></span>
- <span data-ttu-id="c9189-177">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-177">'Identity'</span></span>
- <span data-ttu-id="c9189-178">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-179">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-179">'Razor'</span></span>
- <span data-ttu-id="c9189-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-181">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-183">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-183">'Blazor'</span></span>
- <span data-ttu-id="c9189-184">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-184">'Identity'</span></span>
- <span data-ttu-id="c9189-185">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-186">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-186">'Razor'</span></span>
- <span data-ttu-id="c9189-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-187">'SignalR' uid:</span></span> 

<span data-ttu-id="c9189-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente `App` en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c9189-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9189-189">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="c9189-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c9189-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c9189-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9189-191">La salida del componente `App` no está incluida.</span><span class="sxs-lookup"><span data-stu-id="c9189-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="c9189-192">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="c9189-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c9189-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente `App` en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="c9189-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="c9189-194">Para más información sobre el asistente de etiquetas de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="c9189-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="c9189-195">Agregue una ruta de prioridad baja para la página *_Host.cshtml* a la configuración del punto de conexión en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c9189-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="c9189-196">Agregue componentes enrutables a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c9189-196">Add routable components to the app.</span></span> <span data-ttu-id="c9189-197">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c9189-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="c9189-198">Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="c9189-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="c9189-199">Uso de componentes enrutables en una aplicación MVC</span><span class="sxs-lookup"><span data-stu-id="c9189-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="c9189-200">*Esta sección pertenece a la incorporación de componentes que se pueden enrutar directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="c9189-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="c9189-201">Para admitir componentes Razor enrutables en aplicaciones MVC, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9189-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="c9189-202">Siga las instrucciones que aparecen en la sección [Preparación de la aplicación](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="c9189-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="c9189-203">Agregue un archivo *App.razor* a la raíz del proyecto con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9189-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="c9189-204">Agregue un archivo *_Host.cshtml* a la carpeta *Views/Home* con el contenido siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9189-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="c9189-205">Los componentes usan el archivo compartido *_Layout.cshtml* para su diseño.</span><span class="sxs-lookup"><span data-stu-id="c9189-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="c9189-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura si el componente `App`:</span><span class="sxs-lookup"><span data-stu-id="c9189-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="c9189-207">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="c9189-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="c9189-208">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="c9189-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="c9189-209">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="c9189-209">Render Mode</span></span> | <span data-ttu-id="c9189-210">Descripción</span><span class="sxs-lookup"><span data-stu-id="c9189-210">Description</span></span> |
   | ---
<span data-ttu-id="c9189-211">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-213">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-213">'Blazor'</span></span>
- <span data-ttu-id="c9189-214">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-214">'Identity'</span></span>
- <span data-ttu-id="c9189-215">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-216">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-216">'Razor'</span></span>
- <span data-ttu-id="c9189-217">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-218">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-220">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-220">'Blazor'</span></span>
- <span data-ttu-id="c9189-221">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-221">'Identity'</span></span>
- <span data-ttu-id="c9189-222">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-223">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-223">'Razor'</span></span>
- <span data-ttu-id="c9189-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-225">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-227">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-227">'Blazor'</span></span>
- <span data-ttu-id="c9189-228">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-228">'Identity'</span></span>
- <span data-ttu-id="c9189-229">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-230">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-230">'Razor'</span></span>
- <span data-ttu-id="c9189-231">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-231">'SignalR' uid:</span></span> 

<span data-ttu-id="c9189-232">------ | --- title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-234">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-234">'Blazor'</span></span>
- <span data-ttu-id="c9189-235">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-235">'Identity'</span></span>
- <span data-ttu-id="c9189-236">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-237">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-237">'Razor'</span></span>
- <span data-ttu-id="c9189-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-239">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-241">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-241">'Blazor'</span></span>
- <span data-ttu-id="c9189-242">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-242">'Identity'</span></span>
- <span data-ttu-id="c9189-243">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-244">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-244">'Razor'</span></span>
- <span data-ttu-id="c9189-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c9189-246">title: "Integración de componentes de ASP.NET Core Razor en aplicaciones de Razor Pages y MVC" author: description: "Obtenga información sobre los escenarios de enlace de datos para componentes y elementos DOM en aplicaciones de Blazor".</span><span class="sxs-lookup"><span data-stu-id="c9189-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c9189-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c9189-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c9189-248">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c9189-248">'Blazor'</span></span>
- <span data-ttu-id="c9189-249">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c9189-249">'Identity'</span></span>
- <span data-ttu-id="c9189-250">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c9189-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="c9189-251">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c9189-251">'Razor'</span></span>
- <span data-ttu-id="c9189-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c9189-252">'SignalR' uid:</span></span> 

<span data-ttu-id="c9189-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente `App` en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c9189-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9189-254">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="c9189-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c9189-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c9189-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9189-256">La salida del componente `App` no está incluida.</span><span class="sxs-lookup"><span data-stu-id="c9189-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="c9189-257">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="c9189-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c9189-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente `App` en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="c9189-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="c9189-259">Para más información sobre el asistente de etiquetas de componente, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="c9189-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="c9189-260">Agregue una acción al controlador de inicio:</span><span class="sxs-lookup"><span data-stu-id="c9189-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="c9189-261">Agregue una ruta de prioridad baja para la acción de controlador que devuelve la vista *_Host.cshtml* a la configuración del punto de conexión en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c9189-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="c9189-262">Cree una carpeta *Pages* y agregue componentes enrutables a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c9189-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="c9189-263">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c9189-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="c9189-264">Para obtener más información sobre los espacios de nombres, vea la sección [Espacios de nombres de componentes](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="c9189-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="c9189-265">Representación de componentes a partir de una página o vista</span><span class="sxs-lookup"><span data-stu-id="c9189-265">Render components from a page or view</span></span>

<span data-ttu-id="c9189-266">*Esta sección pertenece a la adición de componentes a páginas o vistas, donde los componentes no son enrutables directamente desde las solicitudes del usuario.*</span><span class="sxs-lookup"><span data-stu-id="c9189-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="c9189-267">Para representar un componente a partir de una página o vista, use el [asistente de etiquetas de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="c9189-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="c9189-268">Representación de componentes interactivos con estado</span><span class="sxs-lookup"><span data-stu-id="c9189-268">Render stateful interactive components</span></span>

<span data-ttu-id="c9189-269">Los componentes interactivos con estado se pueden agregar a una página de Razor o una vista.</span><span class="sxs-lookup"><span data-stu-id="c9189-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="c9189-270">Cuando se representa la página o la vista:</span><span class="sxs-lookup"><span data-stu-id="c9189-270">When the page or view renders:</span></span>

* <span data-ttu-id="c9189-271">El componente se representa previamente con la página o la vista.</span><span class="sxs-lookup"><span data-stu-id="c9189-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="c9189-272">Se pierde el estado inicial del componente que se usa para la representación previa.</span><span class="sxs-lookup"><span data-stu-id="c9189-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="c9189-273">Cuando se establece la conexión SignalR, se crea un estado del componente.</span><span class="sxs-lookup"><span data-stu-id="c9189-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="c9189-274">La siguiente página de Razor representa un componente `Counter`:</span><span class="sxs-lookup"><span data-stu-id="c9189-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="c9189-275">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="c9189-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="c9189-276">Representación de componentes no interactivos</span><span class="sxs-lookup"><span data-stu-id="c9189-276">Render noninteractive components</span></span>

<span data-ttu-id="c9189-277">En la siguiente página de Razor, el componente `Counter` se representa de forma estática con un valor inicial que se especifica mediante un formulario.</span><span class="sxs-lookup"><span data-stu-id="c9189-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="c9189-278">Como el componente se representa de forma estática, no es interactivo:</span><span class="sxs-lookup"><span data-stu-id="c9189-278">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="c9189-279">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="c9189-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="c9189-280">Espacios de nombres de componentes</span><span class="sxs-lookup"><span data-stu-id="c9189-280">Component namespaces</span></span>

<span data-ttu-id="c9189-281">Al usar una carpeta personalizada para contener los componentes de la aplicación, agregue el espacio de nombres que representa la carpeta a la página o vista, o al archivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c9189-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c9189-282">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="c9189-282">In the following example:</span></span>

* <span data-ttu-id="c9189-283">Cambie `MyAppNamespace` en el espacio de nombres de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c9189-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="c9189-284">Si no se usa una carpeta denominada *Components* para contener los componentes, cambie `Components` en la carpeta donde estos se encuentren.</span><span class="sxs-lookup"><span data-stu-id="c9189-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="c9189-285">El archivo *_ViewImports.cshtml* se encuentra en la carpeta *Pages* de una aplicación de Razor Pages o en la carpeta *Views* de una aplicación de MVC.</span><span class="sxs-lookup"><span data-stu-id="c9189-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="c9189-286">Para obtener más información, vea <xref:blazor/components#import-components>.</span><span class="sxs-lookup"><span data-stu-id="c9189-286">For more information, see <xref:blazor/components#import-components>.</span></span>
