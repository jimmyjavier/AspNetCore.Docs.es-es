---
<span data-ttu-id="3b55b-101">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-102">'Blazor'</span></span>
- <span data-ttu-id="3b55b-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-103">'Identity'</span></span>
- <span data-ttu-id="3b55b-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-105">'Razor'</span></span>
- <span data-ttu-id="3b55b-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="3b55b-107">Enrutamiento de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b55b-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="3b55b-108">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3b55b-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3b55b-109">Aprenda a enrutar solicitudes y a usar el componente `NavLink` para crear vínculos de navegación en aplicaciones Blazor.</span><span class="sxs-lookup"><span data-stu-id="3b55b-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="3b55b-110">Integración del enrutamiento de puntos de conexión de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b55b-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="3b55b-111"> Server se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="3b55b-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="3b55b-112">Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con `MapBlazorHub` en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="3b55b-113">La configuración más común consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="3b55b-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="3b55b-114">Convencionalmente, la página del *host* se suele llamar *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3b55b-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="3b55b-115">La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas,</span><span class="sxs-lookup"><span data-stu-id="3b55b-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="3b55b-116">y se tiene en cuenta solo cuando no se encuentran coincidencias en otras rutas.</span><span class="sxs-lookup"><span data-stu-id="3b55b-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="3b55b-117">Esto permite a la aplicación usar otros controladores y páginas sin interferir con la aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="3b55b-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="3b55b-118">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="3b55b-118">Route templates</span></span>

<span data-ttu-id="3b55b-119">El componente `Router` permite el enrutamiento a cada componente con una ruta especificada.</span><span class="sxs-lookup"><span data-stu-id="3b55b-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="3b55b-120">El componente `Router` aparece en el archivo *App.razor*:</span><span class="sxs-lookup"><span data-stu-id="3b55b-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="3b55b-121">Cuando se compila un archivo *.razor* con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>, donde se especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="3b55b-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="3b55b-122">En tiempo de ejecución, el componente `RouteView`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="3b55b-123">Recibe el elemento `RouteData` de `Router` junto con los parámetros deseados.</span><span class="sxs-lookup"><span data-stu-id="3b55b-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="3b55b-124">Representa el componente especificado con su diseño (o un diseño predeterminado opcional) por medio de los parámetros especificados.</span><span class="sxs-lookup"><span data-stu-id="3b55b-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="3b55b-125">Opcionalmente, se puede especificar un parámetro `DefaultLayout` con una clase de diseño para usarlo con aquellos componentes que no tengan especificado un diseño.</span><span class="sxs-lookup"><span data-stu-id="3b55b-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="3b55b-126">Las plantillas de Blazor predeterminadas especifican el componente `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="3b55b-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="3b55b-127">*MainLayout.razor* está en la carpeta *Shared* del proyecto de plantilla.</span><span class="sxs-lookup"><span data-stu-id="3b55b-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="3b55b-128">Para más información sobre los diseños, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="3b55b-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="3b55b-129">Se pueden aplicar varias plantillas de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="3b55b-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="3b55b-130">El siguiente componente atiende las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="3b55b-131">Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo *wwwroot/index.html* (Blazor WebAssembly) o en su archivo *Pages/_Host.cshtml* (Blazor Server) con la ruta de acceso base de la aplicación especificada en el atributo `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="3b55b-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="3b55b-132">Para obtener más información, vea <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="3b55b-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="3b55b-133">Suministro de contenido personalizado cuando no se encuentra contenido</span><span class="sxs-lookup"><span data-stu-id="3b55b-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="3b55b-134">El componente `Router` permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.</span><span class="sxs-lookup"><span data-stu-id="3b55b-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="3b55b-135">En el archivo *App.razor*, establezca el contenido personalizado en el parámetro de plantilla `NotFound` del componente `Router`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="3b55b-136">El contenido de las etiquetas `<NotFound>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="3b55b-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="3b55b-137">Para aplicar un diseño predeterminado al contenido de `NotFound`, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="3b55b-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="3b55b-138">Ruta a componentes desde varios ensamblados</span><span class="sxs-lookup"><span data-stu-id="3b55b-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="3b55b-139">Use el parámetro `AdditionalAssemblies` para especificar más ensamblados para que el componente `Router` los tenga en cuenta al buscar componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="3b55b-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="3b55b-140">Los ensamblados especificados se tendrán en cuenta además del ensamblado especificado por `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="3b55b-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="3b55b-141">En el siguiente ejemplo, `Component1` es un componente enrutable definido en una biblioteca de clases a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="3b55b-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="3b55b-142">El siguiente ejemplo de `AdditionalAssemblies` da como resultado una compatibilidad de enrutamiento con `Component1`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="3b55b-143">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="3b55b-143">Route parameters</span></span>

<span data-ttu-id="3b55b-144">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre (sin distinguir mayúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="3b55b-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="3b55b-145">No se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="3b55b-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="3b55b-146">En el ejemplo anterior se aplican dos directivas `@page`.</span><span class="sxs-lookup"><span data-stu-id="3b55b-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="3b55b-147">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="3b55b-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="3b55b-148">mientras que la segunda `@page` toma el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="3b55b-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="3b55b-149">Restricciones de ruta</span><span class="sxs-lookup"><span data-stu-id="3b55b-149">Route constraints</span></span>

<span data-ttu-id="3b55b-150">Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="3b55b-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="3b55b-151">En el siguiente ejemplo, la ruta al componente `Users` solo coincide en estos casos:</span><span class="sxs-lookup"><span data-stu-id="3b55b-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="3b55b-152">Existe un segmento de ruta `Id` en la dirección URL de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="3b55b-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="3b55b-153">El segmento `Id` es un entero (`int`).</span><span class="sxs-lookup"><span data-stu-id="3b55b-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="3b55b-154">En la siguiente tabla figuran las restricciones de ruta que hay disponibles.</span><span class="sxs-lookup"><span data-stu-id="3b55b-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="3b55b-155">Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, vea la advertencia que aparece después de la tabla.</span><span class="sxs-lookup"><span data-stu-id="3b55b-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="3b55b-156">Restricción</span><span class="sxs-lookup"><span data-stu-id="3b55b-156">Constraint</span></span> | <span data-ttu-id="3b55b-157">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="3b55b-157">Example</span></span>           | <span data-ttu-id="3b55b-158">Coincidencias de ejemplo</span><span class="sxs-lookup"><span data-stu-id="3b55b-158">Example Matches</span></span>                                                                  | <span data-ttu-id="3b55b-159">Invariable</span><span class="sxs-lookup"><span data-stu-id="3b55b-159">Invariant</span></span><br><span data-ttu-id="3b55b-160">referencia cultural</span><span class="sxs-lookup"><span data-stu-id="3b55b-160">culture</span></span><br><span data-ttu-id="3b55b-161">coincidencia</span><span class="sxs-lookup"><span data-stu-id="3b55b-161">matching</span></span> |
| ---
<span data-ttu-id="3b55b-162">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-163">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-163">'Blazor'</span></span>
- <span data-ttu-id="3b55b-164">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-164">'Identity'</span></span>
- <span data-ttu-id="3b55b-165">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-166">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-166">'Razor'</span></span>
- <span data-ttu-id="3b55b-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-168">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-169">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-169">'Blazor'</span></span>
- <span data-ttu-id="3b55b-170">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-170">'Identity'</span></span>
- <span data-ttu-id="3b55b-171">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-172">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-172">'Razor'</span></span>
- <span data-ttu-id="3b55b-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-174">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-175">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-175">'Blazor'</span></span>
- <span data-ttu-id="3b55b-176">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-176">'Identity'</span></span>
- <span data-ttu-id="3b55b-177">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-178">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-178">'Razor'</span></span>
- <span data-ttu-id="3b55b-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-179">'SignalR' uid:</span></span> 

<span data-ttu-id="3b55b-180">----- | --- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-181">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-181">'Blazor'</span></span>
- <span data-ttu-id="3b55b-182">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-182">'Identity'</span></span>
- <span data-ttu-id="3b55b-183">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-184">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-184">'Razor'</span></span>
- <span data-ttu-id="3b55b-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-186">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-187">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-187">'Blazor'</span></span>
- <span data-ttu-id="3b55b-188">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-188">'Identity'</span></span>
- <span data-ttu-id="3b55b-189">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-190">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-190">'Razor'</span></span>
- <span data-ttu-id="3b55b-191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-192">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-193">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-193">'Blazor'</span></span>
- <span data-ttu-id="3b55b-194">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-194">'Identity'</span></span>
- <span data-ttu-id="3b55b-195">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-196">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-196">'Razor'</span></span>
- <span data-ttu-id="3b55b-197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-198">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-199">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-199">'Blazor'</span></span>
- <span data-ttu-id="3b55b-200">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-200">'Identity'</span></span>
- <span data-ttu-id="3b55b-201">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-202">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-202">'Razor'</span></span>
- <span data-ttu-id="3b55b-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-204">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-205">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-205">'Blazor'</span></span>
- <span data-ttu-id="3b55b-206">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-206">'Identity'</span></span>
- <span data-ttu-id="3b55b-207">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-208">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-208">'Razor'</span></span>
- <span data-ttu-id="3b55b-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-210">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-211">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-211">'Blazor'</span></span>
- <span data-ttu-id="3b55b-212">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-212">'Identity'</span></span>
- <span data-ttu-id="3b55b-213">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-214">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-214">'Razor'</span></span>
- <span data-ttu-id="3b55b-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-215">'SignalR' uid:</span></span> 

<span data-ttu-id="3b55b-216">--------- | --- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-217">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-217">'Blazor'</span></span>
- <span data-ttu-id="3b55b-218">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-218">'Identity'</span></span>
- <span data-ttu-id="3b55b-219">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-220">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-220">'Razor'</span></span>
- <span data-ttu-id="3b55b-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-222">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-223">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-223">'Blazor'</span></span>
- <span data-ttu-id="3b55b-224">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-224">'Identity'</span></span>
- <span data-ttu-id="3b55b-225">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-226">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-226">'Razor'</span></span>
- <span data-ttu-id="3b55b-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-228">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-229">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-229">'Blazor'</span></span>
- <span data-ttu-id="3b55b-230">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-230">'Identity'</span></span>
- <span data-ttu-id="3b55b-231">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-232">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-232">'Razor'</span></span>
- <span data-ttu-id="3b55b-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-234">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-235">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-235">'Blazor'</span></span>
- <span data-ttu-id="3b55b-236">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-236">'Identity'</span></span>
- <span data-ttu-id="3b55b-237">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-238">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-238">'Razor'</span></span>
- <span data-ttu-id="3b55b-239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-240">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-241">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-241">'Blazor'</span></span>
- <span data-ttu-id="3b55b-242">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-242">'Identity'</span></span>
- <span data-ttu-id="3b55b-243">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-244">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-244">'Razor'</span></span>
- <span data-ttu-id="3b55b-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-246">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-247">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-247">'Blazor'</span></span>
- <span data-ttu-id="3b55b-248">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-248">'Identity'</span></span>
- <span data-ttu-id="3b55b-249">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-250">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-250">'Razor'</span></span>
- <span data-ttu-id="3b55b-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-252">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-253">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-253">'Blazor'</span></span>
- <span data-ttu-id="3b55b-254">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-254">'Identity'</span></span>
- <span data-ttu-id="3b55b-255">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-256">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-256">'Razor'</span></span>
- <span data-ttu-id="3b55b-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-258">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-259">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-259">'Blazor'</span></span>
- <span data-ttu-id="3b55b-260">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-260">'Identity'</span></span>
- <span data-ttu-id="3b55b-261">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-262">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-262">'Razor'</span></span>
- <span data-ttu-id="3b55b-263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-264">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-265">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-265">'Blazor'</span></span>
- <span data-ttu-id="3b55b-266">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-266">'Identity'</span></span>
- <span data-ttu-id="3b55b-267">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-268">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-268">'Razor'</span></span>
- <span data-ttu-id="3b55b-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-270">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-271">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-271">'Blazor'</span></span>
- <span data-ttu-id="3b55b-272">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-272">'Identity'</span></span>
- <span data-ttu-id="3b55b-273">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-274">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-274">'Razor'</span></span>
- <span data-ttu-id="3b55b-275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-276">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-277">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-277">'Blazor'</span></span>
- <span data-ttu-id="3b55b-278">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-278">'Identity'</span></span>
- <span data-ttu-id="3b55b-279">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-280">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-280">'Razor'</span></span>
- <span data-ttu-id="3b55b-281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-282">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-283">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-283">'Blazor'</span></span>
- <span data-ttu-id="3b55b-284">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-284">'Identity'</span></span>
- <span data-ttu-id="3b55b-285">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-286">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-286">'Razor'</span></span>
- <span data-ttu-id="3b55b-287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-288">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-289">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-289">'Blazor'</span></span>
- <span data-ttu-id="3b55b-290">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-290">'Identity'</span></span>
- <span data-ttu-id="3b55b-291">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-292">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-292">'Razor'</span></span>
- <span data-ttu-id="3b55b-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-294">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-295">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-295">'Blazor'</span></span>
- <span data-ttu-id="3b55b-296">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-296">'Identity'</span></span>
- <span data-ttu-id="3b55b-297">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-298">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-298">'Razor'</span></span>
- <span data-ttu-id="3b55b-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-300">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-301">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-301">'Blazor'</span></span>
- <span data-ttu-id="3b55b-302">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-302">'Identity'</span></span>
- <span data-ttu-id="3b55b-303">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-304">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-304">'Razor'</span></span>
- <span data-ttu-id="3b55b-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-306">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-307">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-307">'Blazor'</span></span>
- <span data-ttu-id="3b55b-308">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-308">'Identity'</span></span>
- <span data-ttu-id="3b55b-309">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-310">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-310">'Razor'</span></span>
- <span data-ttu-id="3b55b-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-312">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-313">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-313">'Blazor'</span></span>
- <span data-ttu-id="3b55b-314">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-314">'Identity'</span></span>
- <span data-ttu-id="3b55b-315">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-316">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-316">'Razor'</span></span>
- <span data-ttu-id="3b55b-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-318">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-319">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-319">'Blazor'</span></span>
- <span data-ttu-id="3b55b-320">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-320">'Identity'</span></span>
- <span data-ttu-id="3b55b-321">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-322">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-322">'Razor'</span></span>
- <span data-ttu-id="3b55b-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-324">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-325">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-325">'Blazor'</span></span>
- <span data-ttu-id="3b55b-326">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-326">'Identity'</span></span>
- <span data-ttu-id="3b55b-327">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-328">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-328">'Razor'</span></span>
- <span data-ttu-id="3b55b-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-330">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-331">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-331">'Blazor'</span></span>
- <span data-ttu-id="3b55b-332">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-332">'Identity'</span></span>
- <span data-ttu-id="3b55b-333">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-334">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-334">'Razor'</span></span>
- <span data-ttu-id="3b55b-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-336">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-337">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-337">'Blazor'</span></span>
- <span data-ttu-id="3b55b-338">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-338">'Identity'</span></span>
- <span data-ttu-id="3b55b-339">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-340">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-340">'Razor'</span></span>
- <span data-ttu-id="3b55b-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-342">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-343">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-343">'Blazor'</span></span>
- <span data-ttu-id="3b55b-344">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-344">'Identity'</span></span>
- <span data-ttu-id="3b55b-345">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-346">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-346">'Razor'</span></span>
- <span data-ttu-id="3b55b-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-348">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-349">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-349">'Blazor'</span></span>
- <span data-ttu-id="3b55b-350">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-350">'Identity'</span></span>
- <span data-ttu-id="3b55b-351">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-352">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-352">'Razor'</span></span>
- <span data-ttu-id="3b55b-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-354">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-355">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-355">'Blazor'</span></span>
- <span data-ttu-id="3b55b-356">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-356">'Identity'</span></span>
- <span data-ttu-id="3b55b-357">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-358">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-358">'Razor'</span></span>
- <span data-ttu-id="3b55b-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-360">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-361">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-361">'Blazor'</span></span>
- <span data-ttu-id="3b55b-362">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-362">'Identity'</span></span>
- <span data-ttu-id="3b55b-363">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-364">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-364">'Razor'</span></span>
- <span data-ttu-id="3b55b-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-366">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-367">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-367">'Blazor'</span></span>
- <span data-ttu-id="3b55b-368">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-368">'Identity'</span></span>
- <span data-ttu-id="3b55b-369">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-370">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-370">'Razor'</span></span>
- <span data-ttu-id="3b55b-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-372">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-373">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-373">'Blazor'</span></span>
- <span data-ttu-id="3b55b-374">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-374">'Identity'</span></span>
- <span data-ttu-id="3b55b-375">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-376">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-376">'Razor'</span></span>
- <span data-ttu-id="3b55b-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-378">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-379">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-379">'Blazor'</span></span>
- <span data-ttu-id="3b55b-380">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-380">'Identity'</span></span>
- <span data-ttu-id="3b55b-381">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-382">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-382">'Razor'</span></span>
- <span data-ttu-id="3b55b-383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-384">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-385">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-385">'Blazor'</span></span>
- <span data-ttu-id="3b55b-386">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-386">'Identity'</span></span>
- <span data-ttu-id="3b55b-387">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-388">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-388">'Razor'</span></span>
- <span data-ttu-id="3b55b-389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-390">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-391">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-391">'Blazor'</span></span>
- <span data-ttu-id="3b55b-392">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-392">'Identity'</span></span>
- <span data-ttu-id="3b55b-393">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-394">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-394">'Razor'</span></span>
- <span data-ttu-id="3b55b-395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-396">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-397">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-397">'Blazor'</span></span>
- <span data-ttu-id="3b55b-398">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-398">'Identity'</span></span>
- <span data-ttu-id="3b55b-399">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-400">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-400">'Razor'</span></span>
- <span data-ttu-id="3b55b-401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-402">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-403">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-403">'Blazor'</span></span>
- <span data-ttu-id="3b55b-404">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-404">'Identity'</span></span>
- <span data-ttu-id="3b55b-405">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-406">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-406">'Razor'</span></span>
- <span data-ttu-id="3b55b-407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-408">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-409">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-409">'Blazor'</span></span>
- <span data-ttu-id="3b55b-410">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-410">'Identity'</span></span>
- <span data-ttu-id="3b55b-411">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-412">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-412">'Razor'</span></span>
- <span data-ttu-id="3b55b-413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-414">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-415">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-415">'Blazor'</span></span>
- <span data-ttu-id="3b55b-416">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-416">'Identity'</span></span>
- <span data-ttu-id="3b55b-417">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-418">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-418">'Razor'</span></span>
- <span data-ttu-id="3b55b-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-420">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-421">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-421">'Blazor'</span></span>
- <span data-ttu-id="3b55b-422">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-422">'Identity'</span></span>
- <span data-ttu-id="3b55b-423">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-424">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-424">'Razor'</span></span>
- <span data-ttu-id="3b55b-425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-426">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-427">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-427">'Blazor'</span></span>
- <span data-ttu-id="3b55b-428">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-428">'Identity'</span></span>
- <span data-ttu-id="3b55b-429">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-430">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-430">'Razor'</span></span>
- <span data-ttu-id="3b55b-431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-432">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-433">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-433">'Blazor'</span></span>
- <span data-ttu-id="3b55b-434">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-434">'Identity'</span></span>
- <span data-ttu-id="3b55b-435">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-436">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-436">'Razor'</span></span>
- <span data-ttu-id="3b55b-437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-438">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-439">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-439">'Blazor'</span></span>
- <span data-ttu-id="3b55b-440">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-440">'Identity'</span></span>
- <span data-ttu-id="3b55b-441">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-442">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-442">'Razor'</span></span>
- <span data-ttu-id="3b55b-443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-443">'SignalR' uid:</span></span> 

<span data-ttu-id="3b55b-444">---------------------------------------- | :--- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-445">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-445">'Blazor'</span></span>
- <span data-ttu-id="3b55b-446">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-446">'Identity'</span></span>
- <span data-ttu-id="3b55b-447">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-448">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-448">'Razor'</span></span>
- <span data-ttu-id="3b55b-449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-450">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-451">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-451">'Blazor'</span></span>
- <span data-ttu-id="3b55b-452">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-452">'Identity'</span></span>
- <span data-ttu-id="3b55b-453">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-454">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-454">'Razor'</span></span>
- <span data-ttu-id="3b55b-455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-456">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-457">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-457">'Blazor'</span></span>
- <span data-ttu-id="3b55b-458">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-458">'Identity'</span></span>
- <span data-ttu-id="3b55b-459">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-460">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-460">'Razor'</span></span>
- <span data-ttu-id="3b55b-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-462">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-463">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-463">'Blazor'</span></span>
- <span data-ttu-id="3b55b-464">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-464">'Identity'</span></span>
- <span data-ttu-id="3b55b-465">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-466">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-466">'Razor'</span></span>
- <span data-ttu-id="3b55b-467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-468">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-469">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-469">'Blazor'</span></span>
- <span data-ttu-id="3b55b-470">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-470">'Identity'</span></span>
- <span data-ttu-id="3b55b-471">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-472">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-472">'Razor'</span></span>
- <span data-ttu-id="3b55b-473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-474">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-475">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-475">'Blazor'</span></span>
- <span data-ttu-id="3b55b-476">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-476">'Identity'</span></span>
- <span data-ttu-id="3b55b-477">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-478">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-478">'Razor'</span></span>
- <span data-ttu-id="3b55b-479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-480">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-481">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-481">'Blazor'</span></span>
- <span data-ttu-id="3b55b-482">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-482">'Identity'</span></span>
- <span data-ttu-id="3b55b-483">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-484">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-484">'Razor'</span></span>
- <span data-ttu-id="3b55b-485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-486">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-487">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-487">'Blazor'</span></span>
- <span data-ttu-id="3b55b-488">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-488">'Identity'</span></span>
- <span data-ttu-id="3b55b-489">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-490">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-490">'Razor'</span></span>
- <span data-ttu-id="3b55b-491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-492">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-493">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-493">'Blazor'</span></span>
- <span data-ttu-id="3b55b-494">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-494">'Identity'</span></span>
- <span data-ttu-id="3b55b-495">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-496">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-496">'Razor'</span></span>
- <span data-ttu-id="3b55b-497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-498">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-499">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-499">'Blazor'</span></span>
- <span data-ttu-id="3b55b-500">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-500">'Identity'</span></span>
- <span data-ttu-id="3b55b-501">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-502">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-502">'Razor'</span></span>
- <span data-ttu-id="3b55b-503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-504">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-505">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-505">'Blazor'</span></span>
- <span data-ttu-id="3b55b-506">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-506">'Identity'</span></span>
- <span data-ttu-id="3b55b-507">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-508">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-508">'Razor'</span></span>
- <span data-ttu-id="3b55b-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-510">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-511">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-511">'Blazor'</span></span>
- <span data-ttu-id="3b55b-512">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-512">'Identity'</span></span>
- <span data-ttu-id="3b55b-513">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-514">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-514">'Razor'</span></span>
- <span data-ttu-id="3b55b-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-516">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-517">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-517">'Blazor'</span></span>
- <span data-ttu-id="3b55b-518">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-518">'Identity'</span></span>
- <span data-ttu-id="3b55b-519">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-520">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-520">'Razor'</span></span>
- <span data-ttu-id="3b55b-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-521">'SignalR' uid:</span></span> 

<span data-ttu-id="3b55b-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sí                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sí                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sí                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sí                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sí                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sí                              |</span><span class="sxs-lookup"><span data-stu-id="3b55b-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="3b55b-523">Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o `DateTime`) usan siempre la referencia cultural invariable.</span><span class="sxs-lookup"><span data-stu-id="3b55b-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="3b55b-524">Estas restricciones dan por supuesto que la dirección URL no es localizable.</span><span class="sxs-lookup"><span data-stu-id="3b55b-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="3b55b-525">Enrutamiento con direcciones URL que contienen puntos</span><span class="sxs-lookup"><span data-stu-id="3b55b-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="3b55b-526">En las aplicaciones Blazor Server, la ruta predeterminada en *_Host.cshtml* es `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="3b55b-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="3b55b-527">Una dirección URL de solicitud que contiene un punto (`.`) no coincide con la ruta predeterminada porque la dirección URL parece que solicita un archivo.</span><span class="sxs-lookup"><span data-stu-id="3b55b-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="3b55b-528">Una aplicación Blazor devuelve una respuesta *404 No encontrado* cuando un archivo estático no existe.</span><span class="sxs-lookup"><span data-stu-id="3b55b-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="3b55b-529">Para usar rutas que contienen un punto, configure *_Host.cshtml* con la siguiente plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="3b55b-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="3b55b-530">La plantilla `"/{**path}"` incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="3b55b-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="3b55b-531">Una sintaxis de *captura general* de doble asterisco (`**`) para capturar la ruta de acceso en varios límites de carpeta sin codificar las barras diagonales (`/`)</span><span class="sxs-lookup"><span data-stu-id="3b55b-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="3b55b-532">El nombre del parámetro de ruta `path`</span><span class="sxs-lookup"><span data-stu-id="3b55b-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="3b55b-533">La sintaxis de parámetros *Catch-all* (`*`/`**`) **no** se admite en componentes Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="3b55b-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="3b55b-534">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="3b55b-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="3b55b-535">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="3b55b-535">NavLink component</span></span>

<span data-ttu-id="3b55b-536">Use un componente `NavLink` en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación.</span><span class="sxs-lookup"><span data-stu-id="3b55b-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="3b55b-537">Un componente `NavLink` se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="3b55b-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="3b55b-538">La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados.</span><span class="sxs-lookup"><span data-stu-id="3b55b-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="3b55b-539">El siguiente componente `NavMenu` crea una barra de navegación de [Bootstrap](https://getbootstrap.com/docs/) que muestra cómo usar componentes `NavLink`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="3b55b-540">Hay dos opciones de `NavLinkMatch` que se pueden asignar al atributo `Match` del elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="3b55b-541">`NavLinkMatch.All`: el elemento `NavLink` estará activo cuando coincida con la dirección URL actual completa.</span><span class="sxs-lookup"><span data-stu-id="3b55b-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="3b55b-542">`NavLinkMatch.Prefix` (*predeterminado*): el elemento `NavLink` estará activo cuando coincida con cualquier prefijo de la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="3b55b-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="3b55b-543">En el ejemplo anterior, el valor de `href=""` del elemento `NavLink` Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="3b55b-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="3b55b-544">El segundo elemento `NavLink` recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `MyComponent` (por ejemplo, `https://localhost:5001/MyComponent` y `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="3b55b-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="3b55b-545">Se pasan más atributos del componente `NavLink` a la etiqueta delimitadora representada.</span><span class="sxs-lookup"><span data-stu-id="3b55b-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="3b55b-546">En el siguiente ejemplo, el componente `NavLink` incluye el atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="3b55b-547">Se representa el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="3b55b-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="3b55b-548">Aplicaciones auxiliares de URI y estado de navegación</span><span class="sxs-lookup"><span data-stu-id="3b55b-548">URI and navigation state helpers</span></span>

<span data-ttu-id="3b55b-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabajar con los URI y con la navegación en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="3b55b-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="3b55b-550">`NavigationManager` proporciona el evento y los métodos que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="3b55b-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="3b55b-551">Miembro</span><span class="sxs-lookup"><span data-stu-id="3b55b-551">Member</span></span> | <span data-ttu-id="3b55b-552">Descripción</span><span class="sxs-lookup"><span data-stu-id="3b55b-552">Description</span></span> |
| ---
<span data-ttu-id="3b55b-553">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-554">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-554">'Blazor'</span></span>
- <span data-ttu-id="3b55b-555">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-555">'Identity'</span></span>
- <span data-ttu-id="3b55b-556">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-557">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-557">'Razor'</span></span>
- <span data-ttu-id="3b55b-558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-558">'SignalR' uid:</span></span> 

<span data-ttu-id="3b55b-559">--- | --- title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-560">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-560">'Blazor'</span></span>
- <span data-ttu-id="3b55b-561">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-561">'Identity'</span></span>
- <span data-ttu-id="3b55b-562">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-563">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-563">'Razor'</span></span>
- <span data-ttu-id="3b55b-564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-565">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-566">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-566">'Blazor'</span></span>
- <span data-ttu-id="3b55b-567">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-567">'Identity'</span></span>
- <span data-ttu-id="3b55b-568">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-569">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-569">'Razor'</span></span>
- <span data-ttu-id="3b55b-570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3b55b-571">title: "Enrutamiento de ASP.NET Core Blazor" author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3b55b-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3b55b-572">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-572">'Blazor'</span></span>
- <span data-ttu-id="3b55b-573">"Identity"</span><span class="sxs-lookup"><span data-stu-id="3b55b-573">'Identity'</span></span>
- <span data-ttu-id="3b55b-574">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="3b55b-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="3b55b-575">"Razor"</span><span class="sxs-lookup"><span data-stu-id="3b55b-575">'Razor'</span></span>
- <span data-ttu-id="3b55b-576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="3b55b-576">'SignalR' uid:</span></span> 

<span data-ttu-id="3b55b-577">------ | | Uri | Obtiene el URI absoluto actual.</span><span class="sxs-lookup"><span data-stu-id="3b55b-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="3b55b-578">| | BaseUri | Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="3b55b-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="3b55b-579">Normalmente, `BaseUri` corresponde al atributo `href` del elemento `<base>` del documento en *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor).</span><span class="sxs-lookup"><span data-stu-id="3b55b-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="3b55b-580">| | NavigateTo | Navega al URI especificado.</span><span class="sxs-lookup"><span data-stu-id="3b55b-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="3b55b-581">Si `forceLoad` es `true`:</span><span class="sxs-lookup"><span data-stu-id="3b55b-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="3b55b-582">El enrutamiento del lado cliente se omite.</span><span class="sxs-lookup"><span data-stu-id="3b55b-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="3b55b-583">Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</span><span class="sxs-lookup"><span data-stu-id="3b55b-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="3b55b-584">| | LocationChanged | Evento que se desencadena cuando la ubicación de navegación ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="3b55b-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="3b55b-585">| | ToAbsoluteUri | Convierte un URI relativo en un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="3b55b-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="3b55b-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Dado un URI base (por ejemplo, un URI previamente devuelto por `GetBaseUri`), convierte un URI absoluto en un URI relativo al prefijo del URI base.</span><span class="sxs-lookup"><span data-stu-id="3b55b-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="3b55b-587">El siguiente componente navega al componente `Counter` de la aplicación cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="3b55b-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="3b55b-588">El componente siguiente controla un evento de cambio de ubicación.</span><span class="sxs-lookup"><span data-stu-id="3b55b-588">The following component handles a location changed event.</span></span> <span data-ttu-id="3b55b-589">El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="3b55b-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="3b55b-590">Al desenlazar el método se permite la recolección de elementos no utilizados del componente.</span><span class="sxs-lookup"><span data-stu-id="3b55b-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="3b55b-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre el evento:</span><span class="sxs-lookup"><span data-stu-id="3b55b-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="3b55b-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="3b55b-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="3b55b-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador.</span><span class="sxs-lookup"><span data-stu-id="3b55b-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="3b55b-594">Si es `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha provocado que se produjera la navegación.</span><span class="sxs-lookup"><span data-stu-id="3b55b-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="3b55b-595">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="3b55b-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
