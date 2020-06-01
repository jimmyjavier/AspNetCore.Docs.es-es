---
<span data-ttu-id="ecc30-101">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-102">'Blazor'</span></span>
- <span data-ttu-id="ecc30-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-103">'Identity'</span></span>
- <span data-ttu-id="ecc30-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-105">'Razor'</span></span>
- <span data-ttu-id="ecc30-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="ecc30-107">Enrutamiento de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecc30-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="ecc30-108">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ecc30-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ecc30-109">Aprenda a enrutar solicitudes y a usar el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> para crear vínculos de navegación en aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="ecc30-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="ecc30-110">Integración del enrutamiento de puntos de conexión de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecc30-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="ecc30-111"> Server se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="ecc30-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="ecc30-112">Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ecc30-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="ecc30-113">La configuración más común consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ecc30-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="ecc30-114">Convencionalmente, la página del *host* se suele llamar *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ecc30-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="ecc30-115">La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas,</span><span class="sxs-lookup"><span data-stu-id="ecc30-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="ecc30-116">y se tiene en cuenta solo cuando no se encuentran coincidencias en otras rutas.</span><span class="sxs-lookup"><span data-stu-id="ecc30-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="ecc30-117">Esto permite a la aplicación usar otros controladores y páginas sin interferir con la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ecc30-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="ecc30-118">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="ecc30-118">Route templates</span></span>

<span data-ttu-id="ecc30-119">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite el enrutamiento a cada componente con una ruta especificada.</span><span class="sxs-lookup"><span data-stu-id="ecc30-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="ecc30-120">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> aparece en el archivo *App.razor*:</span><span class="sxs-lookup"><span data-stu-id="ecc30-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="ecc30-121">Cuando se compila un archivo *.razor* con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>, donde se especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="ecc30-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="ecc30-122">En tiempo de ejecución, el componente <xref:Microsoft.AspNetCore.Components.RouteView>:</span><span class="sxs-lookup"><span data-stu-id="ecc30-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="ecc30-123">Recibe el elemento <xref:Microsoft.AspNetCore.Components.RouteData> de <xref:Microsoft.AspNetCore.Components.Routing.Router> junto con los parámetros deseados.</span><span class="sxs-lookup"><span data-stu-id="ecc30-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="ecc30-124">Representa el componente especificado con su diseño (o un diseño predeterminado opcional) por medio de los parámetros especificados.</span><span class="sxs-lookup"><span data-stu-id="ecc30-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="ecc30-125">Opcionalmente, se puede especificar un parámetro <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> con una clase de diseño para usarlo con aquellos componentes que no tengan especificado un diseño.</span><span class="sxs-lookup"><span data-stu-id="ecc30-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="ecc30-126">Las plantillas de Blazor predeterminadas especifican el componente `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="ecc30-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="ecc30-127">*MainLayout.razor* está en la carpeta *Shared* del proyecto de plantilla.</span><span class="sxs-lookup"><span data-stu-id="ecc30-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="ecc30-128">Para más información sobre los diseños, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="ecc30-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="ecc30-129">Se pueden aplicar varias plantillas de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="ecc30-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ecc30-130">El siguiente componente atiende las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="ecc30-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="ecc30-131">Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo *wwwroot/index.html* (Blazor WebAssembly) o en su archivo *Pages/_Host.cshtml* (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="ecc30-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="ecc30-132">Para obtener más información, vea <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="ecc30-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="ecc30-133">Suministro de contenido personalizado cuando no se encuentra contenido</span><span class="sxs-lookup"><span data-stu-id="ecc30-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="ecc30-134">El componente <xref:Microsoft.AspNetCore.Components.Routing.Router> permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.</span><span class="sxs-lookup"><span data-stu-id="ecc30-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="ecc30-135">En el archivo *App.razor*, establezca el contenido personalizado en el parámetro de plantilla <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> del componente <xref:Microsoft.AspNetCore.Components.Routing.Router>:</span><span class="sxs-lookup"><span data-stu-id="ecc30-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="ecc30-136">El contenido de las etiquetas `<NotFound>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="ecc30-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="ecc30-137">Para aplicar un diseño predeterminado al contenido de <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="ecc30-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="ecc30-138">Ruta a componentes desde varios ensamblados</span><span class="sxs-lookup"><span data-stu-id="ecc30-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="ecc30-139">Use el parámetro <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> para especificar más ensamblados para que el componente <xref:Microsoft.AspNetCore.Components.Routing.Router> los tenga en cuenta al buscar componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="ecc30-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="ecc30-140">Los ensamblados especificados se tendrán en cuenta además del ensamblado especificado por `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="ecc30-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="ecc30-141">En el siguiente ejemplo, `Component1` es un componente enrutable definido en una biblioteca de clases a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="ecc30-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="ecc30-142">El siguiente ejemplo de <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> da como resultado una compatibilidad de enrutamiento con `Component1`:</span><span class="sxs-lookup"><span data-stu-id="ecc30-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="ecc30-143">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="ecc30-143">Route parameters</span></span>

<span data-ttu-id="ecc30-144">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre (sin distinguir mayúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="ecc30-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="ecc30-145">No se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="ecc30-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="ecc30-146">En el ejemplo anterior se aplican dos directivas `@page`.</span><span class="sxs-lookup"><span data-stu-id="ecc30-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="ecc30-147">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="ecc30-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ecc30-148">mientras que la segunda `@page` toma el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="ecc30-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="ecc30-149">Restricciones de ruta</span><span class="sxs-lookup"><span data-stu-id="ecc30-149">Route constraints</span></span>

<span data-ttu-id="ecc30-150">Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="ecc30-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="ecc30-151">En el siguiente ejemplo, la ruta al componente `Users` solo coincide en estos casos:</span><span class="sxs-lookup"><span data-stu-id="ecc30-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="ecc30-152">Existe un segmento de ruta `Id` en la dirección URL de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ecc30-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="ecc30-153">El segmento `Id` es un entero (`int`).</span><span class="sxs-lookup"><span data-stu-id="ecc30-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="ecc30-154">En la siguiente tabla figuran las restricciones de ruta que hay disponibles.</span><span class="sxs-lookup"><span data-stu-id="ecc30-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="ecc30-155">Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, vea la advertencia que aparece después de la tabla.</span><span class="sxs-lookup"><span data-stu-id="ecc30-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="ecc30-156">Restricción</span><span class="sxs-lookup"><span data-stu-id="ecc30-156">Constraint</span></span> | <span data-ttu-id="ecc30-157">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ecc30-157">Example</span></span>           | <span data-ttu-id="ecc30-158">Coincidencias de ejemplo</span><span class="sxs-lookup"><span data-stu-id="ecc30-158">Example Matches</span></span>                                                                  | <span data-ttu-id="ecc30-159">Invariable</span><span class="sxs-lookup"><span data-stu-id="ecc30-159">Invariant</span></span><br><span data-ttu-id="ecc30-160">referencia cultural</span><span class="sxs-lookup"><span data-stu-id="ecc30-160">culture</span></span><br><span data-ttu-id="ecc30-161">coincidencia</span><span class="sxs-lookup"><span data-stu-id="ecc30-161">matching</span></span> |
| ---
<span data-ttu-id="ecc30-162">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-163">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-163">'Blazor'</span></span>
- <span data-ttu-id="ecc30-164">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-164">'Identity'</span></span>
- <span data-ttu-id="ecc30-165">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-166">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-166">'Razor'</span></span>
- <span data-ttu-id="ecc30-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-168">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-169">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-169">'Blazor'</span></span>
- <span data-ttu-id="ecc30-170">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-170">'Identity'</span></span>
- <span data-ttu-id="ecc30-171">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-172">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-172">'Razor'</span></span>
- <span data-ttu-id="ecc30-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-174">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-175">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-175">'Blazor'</span></span>
- <span data-ttu-id="ecc30-176">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-176">'Identity'</span></span>
- <span data-ttu-id="ecc30-177">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-178">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-178">'Razor'</span></span>
- <span data-ttu-id="ecc30-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-179">'SignalR' uid:</span></span> 

<span data-ttu-id="ecc30-180">----- | --- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-181">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-181">'Blazor'</span></span>
- <span data-ttu-id="ecc30-182">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-182">'Identity'</span></span>
- <span data-ttu-id="ecc30-183">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-184">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-184">'Razor'</span></span>
- <span data-ttu-id="ecc30-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-186">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-187">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-187">'Blazor'</span></span>
- <span data-ttu-id="ecc30-188">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-188">'Identity'</span></span>
- <span data-ttu-id="ecc30-189">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-190">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-190">'Razor'</span></span>
- <span data-ttu-id="ecc30-191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-192">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-193">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-193">'Blazor'</span></span>
- <span data-ttu-id="ecc30-194">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-194">'Identity'</span></span>
- <span data-ttu-id="ecc30-195">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-196">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-196">'Razor'</span></span>
- <span data-ttu-id="ecc30-197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-198">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-199">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-199">'Blazor'</span></span>
- <span data-ttu-id="ecc30-200">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-200">'Identity'</span></span>
- <span data-ttu-id="ecc30-201">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-202">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-202">'Razor'</span></span>
- <span data-ttu-id="ecc30-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-204">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-205">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-205">'Blazor'</span></span>
- <span data-ttu-id="ecc30-206">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-206">'Identity'</span></span>
- <span data-ttu-id="ecc30-207">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-208">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-208">'Razor'</span></span>
- <span data-ttu-id="ecc30-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-210">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-211">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-211">'Blazor'</span></span>
- <span data-ttu-id="ecc30-212">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-212">'Identity'</span></span>
- <span data-ttu-id="ecc30-213">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-214">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-214">'Razor'</span></span>
- <span data-ttu-id="ecc30-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-215">'SignalR' uid:</span></span> 

<span data-ttu-id="ecc30-216">--------- | --- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-217">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-217">'Blazor'</span></span>
- <span data-ttu-id="ecc30-218">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-218">'Identity'</span></span>
- <span data-ttu-id="ecc30-219">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-220">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-220">'Razor'</span></span>
- <span data-ttu-id="ecc30-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-222">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-223">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-223">'Blazor'</span></span>
- <span data-ttu-id="ecc30-224">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-224">'Identity'</span></span>
- <span data-ttu-id="ecc30-225">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-226">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-226">'Razor'</span></span>
- <span data-ttu-id="ecc30-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-228">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-229">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-229">'Blazor'</span></span>
- <span data-ttu-id="ecc30-230">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-230">'Identity'</span></span>
- <span data-ttu-id="ecc30-231">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-232">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-232">'Razor'</span></span>
- <span data-ttu-id="ecc30-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-234">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-235">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-235">'Blazor'</span></span>
- <span data-ttu-id="ecc30-236">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-236">'Identity'</span></span>
- <span data-ttu-id="ecc30-237">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-238">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-238">'Razor'</span></span>
- <span data-ttu-id="ecc30-239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-240">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-241">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-241">'Blazor'</span></span>
- <span data-ttu-id="ecc30-242">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-242">'Identity'</span></span>
- <span data-ttu-id="ecc30-243">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-244">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-244">'Razor'</span></span>
- <span data-ttu-id="ecc30-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-246">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-247">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-247">'Blazor'</span></span>
- <span data-ttu-id="ecc30-248">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-248">'Identity'</span></span>
- <span data-ttu-id="ecc30-249">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-250">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-250">'Razor'</span></span>
- <span data-ttu-id="ecc30-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-252">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-253">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-253">'Blazor'</span></span>
- <span data-ttu-id="ecc30-254">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-254">'Identity'</span></span>
- <span data-ttu-id="ecc30-255">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-256">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-256">'Razor'</span></span>
- <span data-ttu-id="ecc30-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-258">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-259">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-259">'Blazor'</span></span>
- <span data-ttu-id="ecc30-260">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-260">'Identity'</span></span>
- <span data-ttu-id="ecc30-261">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-262">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-262">'Razor'</span></span>
- <span data-ttu-id="ecc30-263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-264">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-265">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-265">'Blazor'</span></span>
- <span data-ttu-id="ecc30-266">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-266">'Identity'</span></span>
- <span data-ttu-id="ecc30-267">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-268">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-268">'Razor'</span></span>
- <span data-ttu-id="ecc30-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-270">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-271">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-271">'Blazor'</span></span>
- <span data-ttu-id="ecc30-272">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-272">'Identity'</span></span>
- <span data-ttu-id="ecc30-273">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-274">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-274">'Razor'</span></span>
- <span data-ttu-id="ecc30-275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-276">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-277">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-277">'Blazor'</span></span>
- <span data-ttu-id="ecc30-278">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-278">'Identity'</span></span>
- <span data-ttu-id="ecc30-279">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-280">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-280">'Razor'</span></span>
- <span data-ttu-id="ecc30-281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-282">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-283">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-283">'Blazor'</span></span>
- <span data-ttu-id="ecc30-284">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-284">'Identity'</span></span>
- <span data-ttu-id="ecc30-285">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-286">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-286">'Razor'</span></span>
- <span data-ttu-id="ecc30-287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-288">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-289">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-289">'Blazor'</span></span>
- <span data-ttu-id="ecc30-290">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-290">'Identity'</span></span>
- <span data-ttu-id="ecc30-291">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-292">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-292">'Razor'</span></span>
- <span data-ttu-id="ecc30-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-294">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-295">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-295">'Blazor'</span></span>
- <span data-ttu-id="ecc30-296">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-296">'Identity'</span></span>
- <span data-ttu-id="ecc30-297">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-298">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-298">'Razor'</span></span>
- <span data-ttu-id="ecc30-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-300">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-301">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-301">'Blazor'</span></span>
- <span data-ttu-id="ecc30-302">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-302">'Identity'</span></span>
- <span data-ttu-id="ecc30-303">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-304">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-304">'Razor'</span></span>
- <span data-ttu-id="ecc30-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-306">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-307">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-307">'Blazor'</span></span>
- <span data-ttu-id="ecc30-308">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-308">'Identity'</span></span>
- <span data-ttu-id="ecc30-309">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-310">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-310">'Razor'</span></span>
- <span data-ttu-id="ecc30-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-312">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-313">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-313">'Blazor'</span></span>
- <span data-ttu-id="ecc30-314">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-314">'Identity'</span></span>
- <span data-ttu-id="ecc30-315">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-316">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-316">'Razor'</span></span>
- <span data-ttu-id="ecc30-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-318">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-319">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-319">'Blazor'</span></span>
- <span data-ttu-id="ecc30-320">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-320">'Identity'</span></span>
- <span data-ttu-id="ecc30-321">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-322">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-322">'Razor'</span></span>
- <span data-ttu-id="ecc30-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-324">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-325">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-325">'Blazor'</span></span>
- <span data-ttu-id="ecc30-326">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-326">'Identity'</span></span>
- <span data-ttu-id="ecc30-327">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-328">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-328">'Razor'</span></span>
- <span data-ttu-id="ecc30-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-330">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-331">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-331">'Blazor'</span></span>
- <span data-ttu-id="ecc30-332">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-332">'Identity'</span></span>
- <span data-ttu-id="ecc30-333">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-334">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-334">'Razor'</span></span>
- <span data-ttu-id="ecc30-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-336">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-337">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-337">'Blazor'</span></span>
- <span data-ttu-id="ecc30-338">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-338">'Identity'</span></span>
- <span data-ttu-id="ecc30-339">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-340">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-340">'Razor'</span></span>
- <span data-ttu-id="ecc30-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-342">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-343">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-343">'Blazor'</span></span>
- <span data-ttu-id="ecc30-344">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-344">'Identity'</span></span>
- <span data-ttu-id="ecc30-345">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-346">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-346">'Razor'</span></span>
- <span data-ttu-id="ecc30-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-348">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-349">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-349">'Blazor'</span></span>
- <span data-ttu-id="ecc30-350">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-350">'Identity'</span></span>
- <span data-ttu-id="ecc30-351">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-352">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-352">'Razor'</span></span>
- <span data-ttu-id="ecc30-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-354">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-355">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-355">'Blazor'</span></span>
- <span data-ttu-id="ecc30-356">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-356">'Identity'</span></span>
- <span data-ttu-id="ecc30-357">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-358">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-358">'Razor'</span></span>
- <span data-ttu-id="ecc30-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-360">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-361">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-361">'Blazor'</span></span>
- <span data-ttu-id="ecc30-362">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-362">'Identity'</span></span>
- <span data-ttu-id="ecc30-363">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-364">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-364">'Razor'</span></span>
- <span data-ttu-id="ecc30-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-366">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-367">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-367">'Blazor'</span></span>
- <span data-ttu-id="ecc30-368">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-368">'Identity'</span></span>
- <span data-ttu-id="ecc30-369">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-370">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-370">'Razor'</span></span>
- <span data-ttu-id="ecc30-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-372">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-373">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-373">'Blazor'</span></span>
- <span data-ttu-id="ecc30-374">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-374">'Identity'</span></span>
- <span data-ttu-id="ecc30-375">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-376">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-376">'Razor'</span></span>
- <span data-ttu-id="ecc30-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-378">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-379">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-379">'Blazor'</span></span>
- <span data-ttu-id="ecc30-380">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-380">'Identity'</span></span>
- <span data-ttu-id="ecc30-381">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-382">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-382">'Razor'</span></span>
- <span data-ttu-id="ecc30-383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-384">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-385">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-385">'Blazor'</span></span>
- <span data-ttu-id="ecc30-386">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-386">'Identity'</span></span>
- <span data-ttu-id="ecc30-387">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-388">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-388">'Razor'</span></span>
- <span data-ttu-id="ecc30-389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-390">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-391">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-391">'Blazor'</span></span>
- <span data-ttu-id="ecc30-392">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-392">'Identity'</span></span>
- <span data-ttu-id="ecc30-393">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-394">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-394">'Razor'</span></span>
- <span data-ttu-id="ecc30-395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-396">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-397">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-397">'Blazor'</span></span>
- <span data-ttu-id="ecc30-398">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-398">'Identity'</span></span>
- <span data-ttu-id="ecc30-399">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-400">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-400">'Razor'</span></span>
- <span data-ttu-id="ecc30-401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-402">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-403">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-403">'Blazor'</span></span>
- <span data-ttu-id="ecc30-404">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-404">'Identity'</span></span>
- <span data-ttu-id="ecc30-405">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-406">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-406">'Razor'</span></span>
- <span data-ttu-id="ecc30-407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-408">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-409">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-409">'Blazor'</span></span>
- <span data-ttu-id="ecc30-410">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-410">'Identity'</span></span>
- <span data-ttu-id="ecc30-411">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-412">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-412">'Razor'</span></span>
- <span data-ttu-id="ecc30-413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-414">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-415">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-415">'Blazor'</span></span>
- <span data-ttu-id="ecc30-416">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-416">'Identity'</span></span>
- <span data-ttu-id="ecc30-417">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-418">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-418">'Razor'</span></span>
- <span data-ttu-id="ecc30-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-420">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-421">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-421">'Blazor'</span></span>
- <span data-ttu-id="ecc30-422">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-422">'Identity'</span></span>
- <span data-ttu-id="ecc30-423">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-424">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-424">'Razor'</span></span>
- <span data-ttu-id="ecc30-425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-426">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-427">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-427">'Blazor'</span></span>
- <span data-ttu-id="ecc30-428">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-428">'Identity'</span></span>
- <span data-ttu-id="ecc30-429">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-430">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-430">'Razor'</span></span>
- <span data-ttu-id="ecc30-431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-432">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-433">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-433">'Blazor'</span></span>
- <span data-ttu-id="ecc30-434">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-434">'Identity'</span></span>
- <span data-ttu-id="ecc30-435">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-436">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-436">'Razor'</span></span>
- <span data-ttu-id="ecc30-437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-438">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-439">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-439">'Blazor'</span></span>
- <span data-ttu-id="ecc30-440">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-440">'Identity'</span></span>
- <span data-ttu-id="ecc30-441">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-442">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-442">'Razor'</span></span>
- <span data-ttu-id="ecc30-443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-443">'SignalR' uid:</span></span> 

<span data-ttu-id="ecc30-444">---------------------------------------- | :--- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-445">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-445">'Blazor'</span></span>
- <span data-ttu-id="ecc30-446">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-446">'Identity'</span></span>
- <span data-ttu-id="ecc30-447">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-448">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-448">'Razor'</span></span>
- <span data-ttu-id="ecc30-449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-450">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-451">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-451">'Blazor'</span></span>
- <span data-ttu-id="ecc30-452">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-452">'Identity'</span></span>
- <span data-ttu-id="ecc30-453">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-454">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-454">'Razor'</span></span>
- <span data-ttu-id="ecc30-455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-456">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-457">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-457">'Blazor'</span></span>
- <span data-ttu-id="ecc30-458">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-458">'Identity'</span></span>
- <span data-ttu-id="ecc30-459">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-460">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-460">'Razor'</span></span>
- <span data-ttu-id="ecc30-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-462">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-463">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-463">'Blazor'</span></span>
- <span data-ttu-id="ecc30-464">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-464">'Identity'</span></span>
- <span data-ttu-id="ecc30-465">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-466">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-466">'Razor'</span></span>
- <span data-ttu-id="ecc30-467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-468">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-469">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-469">'Blazor'</span></span>
- <span data-ttu-id="ecc30-470">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-470">'Identity'</span></span>
- <span data-ttu-id="ecc30-471">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-472">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-472">'Razor'</span></span>
- <span data-ttu-id="ecc30-473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-474">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-475">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-475">'Blazor'</span></span>
- <span data-ttu-id="ecc30-476">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-476">'Identity'</span></span>
- <span data-ttu-id="ecc30-477">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-478">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-478">'Razor'</span></span>
- <span data-ttu-id="ecc30-479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-480">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-481">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-481">'Blazor'</span></span>
- <span data-ttu-id="ecc30-482">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-482">'Identity'</span></span>
- <span data-ttu-id="ecc30-483">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-484">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-484">'Razor'</span></span>
- <span data-ttu-id="ecc30-485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-486">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-487">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-487">'Blazor'</span></span>
- <span data-ttu-id="ecc30-488">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-488">'Identity'</span></span>
- <span data-ttu-id="ecc30-489">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-490">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-490">'Razor'</span></span>
- <span data-ttu-id="ecc30-491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-492">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-493">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-493">'Blazor'</span></span>
- <span data-ttu-id="ecc30-494">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-494">'Identity'</span></span>
- <span data-ttu-id="ecc30-495">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-496">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-496">'Razor'</span></span>
- <span data-ttu-id="ecc30-497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-498">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-499">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-499">'Blazor'</span></span>
- <span data-ttu-id="ecc30-500">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-500">'Identity'</span></span>
- <span data-ttu-id="ecc30-501">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-502">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-502">'Razor'</span></span>
- <span data-ttu-id="ecc30-503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-504">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-505">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-505">'Blazor'</span></span>
- <span data-ttu-id="ecc30-506">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-506">'Identity'</span></span>
- <span data-ttu-id="ecc30-507">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-508">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-508">'Razor'</span></span>
- <span data-ttu-id="ecc30-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-510">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-511">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-511">'Blazor'</span></span>
- <span data-ttu-id="ecc30-512">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-512">'Identity'</span></span>
- <span data-ttu-id="ecc30-513">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-514">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-514">'Razor'</span></span>
- <span data-ttu-id="ecc30-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-516">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-517">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-517">'Blazor'</span></span>
- <span data-ttu-id="ecc30-518">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-518">'Identity'</span></span>
- <span data-ttu-id="ecc30-519">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-520">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-520">'Razor'</span></span>
- <span data-ttu-id="ecc30-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-521">'SignalR' uid:</span></span> 

<span data-ttu-id="ecc30-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sí                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sí                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sí                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sí                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sí                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sí                              |</span><span class="sxs-lookup"><span data-stu-id="ecc30-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="ecc30-523">Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o <xref:System.DateTime>) usan siempre la referencia cultural invariable.</span><span class="sxs-lookup"><span data-stu-id="ecc30-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="ecc30-524">Estas restricciones dan por supuesto que la dirección URL no es localizable.</span><span class="sxs-lookup"><span data-stu-id="ecc30-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="ecc30-525">Enrutamiento con direcciones URL que contienen puntos</span><span class="sxs-lookup"><span data-stu-id="ecc30-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="ecc30-526">En las aplicaciones Blazor Server, la ruta predeterminada en *_Host.cshtml* es `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="ecc30-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="ecc30-527">Una dirección URL de solicitud que contiene un punto (`.`) no coincide con la ruta predeterminada porque la dirección URL parece que solicita un archivo.</span><span class="sxs-lookup"><span data-stu-id="ecc30-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="ecc30-528">Una aplicación Blazor devuelve una respuesta *404 No encontrado* cuando un archivo estático no existe.</span><span class="sxs-lookup"><span data-stu-id="ecc30-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="ecc30-529">Para usar rutas que contienen un punto, configure *_Host.cshtml* con la siguiente plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="ecc30-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="ecc30-530">La plantilla `"/{**path}"` incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecc30-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="ecc30-531">Una sintaxis de *captura general* de doble asterisco (`**`) para capturar la ruta de acceso en varios límites de carpeta sin codificar las barras diagonales (`/`)</span><span class="sxs-lookup"><span data-stu-id="ecc30-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="ecc30-532">El nombre del parámetro de ruta `path`</span><span class="sxs-lookup"><span data-stu-id="ecc30-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="ecc30-533">La sintaxis de parámetros *Catch-all* (`*`/`**`) **no** se admite en componentes Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="ecc30-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="ecc30-534">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="ecc30-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="ecc30-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="ecc30-535">NavLink component</span></span>

<span data-ttu-id="ecc30-536">Use un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación.</span><span class="sxs-lookup"><span data-stu-id="ecc30-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="ecc30-537">Un componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="ecc30-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="ecc30-538">La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados.</span><span class="sxs-lookup"><span data-stu-id="ecc30-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="ecc30-539">El siguiente componente `NavMenu` crea una barra de navegación de [Bootstrap](https://getbootstrap.com/docs/) que muestra cómo usar componentes <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:</span><span class="sxs-lookup"><span data-stu-id="ecc30-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="ecc30-540">Hay dos opciones de <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> que se pueden asignar al atributo `Match` del elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="ecc30-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="ecc30-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con la dirección URL actual completa.</span><span class="sxs-lookup"><span data-stu-id="ecc30-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="ecc30-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*predeterminado*): el elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> estará activo cuando coincida con cualquier prefijo de la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="ecc30-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="ecc30-543">En el ejemplo anterior, el valor de `href=""` del elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="ecc30-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="ecc30-544">El segundo elemento <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `MyComponent` (por ejemplo, `https://localhost:5001/MyComponent` y `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="ecc30-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="ecc30-545">Se pasan más atributos del componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> a la etiqueta delimitadora representada.</span><span class="sxs-lookup"><span data-stu-id="ecc30-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="ecc30-546">En el siguiente ejemplo, el componente <xref:Microsoft.AspNetCore.Components.Routing.NavLink> incluye el atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="ecc30-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="ecc30-547">Se representa el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="ecc30-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="ecc30-548">Aplicaciones auxiliares de URI y estado de navegación</span><span class="sxs-lookup"><span data-stu-id="ecc30-548">URI and navigation state helpers</span></span>

<span data-ttu-id="ecc30-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabajar con los URI y con la navegación en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="ecc30-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="ecc30-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> proporciona el evento y los métodos que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="ecc30-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="ecc30-551">Miembro</span><span class="sxs-lookup"><span data-stu-id="ecc30-551">Member</span></span> | <span data-ttu-id="ecc30-552">Descripción</span><span class="sxs-lookup"><span data-stu-id="ecc30-552">Description</span></span> |
| ---
<span data-ttu-id="ecc30-553">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-554">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-554">'Blazor'</span></span>
- <span data-ttu-id="ecc30-555">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-555">'Identity'</span></span>
- <span data-ttu-id="ecc30-556">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-557">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-557">'Razor'</span></span>
- <span data-ttu-id="ecc30-558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-558">'SignalR' uid:</span></span> 

<span data-ttu-id="ecc30-559">--- | --- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-560">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-560">'Blazor'</span></span>
- <span data-ttu-id="ecc30-561">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-561">'Identity'</span></span>
- <span data-ttu-id="ecc30-562">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-563">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-563">'Razor'</span></span>
- <span data-ttu-id="ecc30-564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-565">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-566">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-566">'Blazor'</span></span>
- <span data-ttu-id="ecc30-567">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-567">'Identity'</span></span>
- <span data-ttu-id="ecc30-568">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-569">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-569">'Razor'</span></span>
- <span data-ttu-id="ecc30-570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ecc30-571">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ecc30-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ecc30-572">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-572">'Blazor'</span></span>
- <span data-ttu-id="ecc30-573">"Identity"</span><span class="sxs-lookup"><span data-stu-id="ecc30-573">'Identity'</span></span>
- <span data-ttu-id="ecc30-574">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="ecc30-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="ecc30-575">"Razor"</span><span class="sxs-lookup"><span data-stu-id="ecc30-575">'Razor'</span></span>
- <span data-ttu-id="ecc30-576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="ecc30-576">'SignalR' uid:</span></span> 

<span data-ttu-id="ecc30-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Obtiene el URI absoluto actual.</span><span class="sxs-lookup"><span data-stu-id="ecc30-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="ecc30-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="ecc30-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="ecc30-579">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde al atributo `href` del elemento `<base>` del documento en *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor).</span><span class="sxs-lookup"><span data-stu-id="ecc30-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="ecc30-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navega al URI especificado.</span><span class="sxs-lookup"><span data-stu-id="ecc30-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="ecc30-581">Si `forceLoad` es `true`:</span><span class="sxs-lookup"><span data-stu-id="ecc30-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="ecc30-582">El enrutamiento del lado cliente se omite.</span><span class="sxs-lookup"><span data-stu-id="ecc30-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="ecc30-583">Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</span><span class="sxs-lookup"><span data-stu-id="ecc30-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="ecc30-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Evento que se desencadena cuando la ubicación de navegación ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="ecc30-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="ecc30-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Convierte un URI relativo en un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="ecc30-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="ecc30-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Dado un URI base (por ejemplo, un URI previamente devuelto por <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), convierte un URI absoluto en un URI relativo al prefijo de URI base.</span><span class="sxs-lookup"><span data-stu-id="ecc30-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="ecc30-587">El siguiente componente navega al componente `Counter` de la aplicación cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="ecc30-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="ecc30-588">El componente siguiente controla un evento de cambio de ubicación estableciendo <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ecc30-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ecc30-589">El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="ecc30-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="ecc30-590">Al desenlazar el método se permite la recolección de elementos no utilizados del componente.</span><span class="sxs-lookup"><span data-stu-id="ecc30-590">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="ecc30-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre el evento:</span><span class="sxs-lookup"><span data-stu-id="ecc30-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="ecc30-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="ecc30-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="ecc30-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador.</span><span class="sxs-lookup"><span data-stu-id="ecc30-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="ecc30-594">Si `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> hizo que se produjera la navegación.</span><span class="sxs-lookup"><span data-stu-id="ecc30-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="ecc30-595">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="ecc30-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
