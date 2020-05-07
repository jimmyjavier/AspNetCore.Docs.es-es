---
title: Enrutamiento de Blazor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo enrutar solicitudes en aplicaciones y sobre el componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/routing
ms.openlocfilehash: 017fd4d3ab45b75355dabb400ff0e5cbf7009d82
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771225"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="ef470-103">Enrutamiento de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef470-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="ef470-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ef470-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ef470-105">Obtenga información sobre cómo enrutar solicitudes y cómo usar el componente `NavLink` para crear vínculos de navegación en aplicaciones de Blazor.</span><span class="sxs-lookup"><span data-stu-id="ef470-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="ef470-106">Integración del enrutamiento de puntos de conexión de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ef470-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="ef470-107">El servidor Blazor se integra en el [enrutamiento de puntos de conexión de ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="ef470-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="ef470-108">Una aplicación ASP.NET Core está configurada para aceptar conexiones entrantes de componentes interactivos con `MapBlazorHub` en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ef470-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="ef470-109">La configuración más común consiste en enrutar todas las solicitudes a una página de Razor, que actúa como el host del lado servidor de la aplicación del servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="ef470-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="ef470-110">Convencionalmente, la página del *host* se suele llamar *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ef470-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="ef470-111">La ruta especificada en el archivo de host se denomina *ruta de reserva* porque tiene una prioridad baja en la búsqueda de rutas,</span><span class="sxs-lookup"><span data-stu-id="ef470-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="ef470-112">y se tiene en cuenta solo cuando no se encuentran coincidencias en otras rutas.</span><span class="sxs-lookup"><span data-stu-id="ef470-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="ef470-113">Esto permite a la aplicación usar otros controladores y páginas sin interferir con la aplicación de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="ef470-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="ef470-114">Plantillas de ruta</span><span class="sxs-lookup"><span data-stu-id="ef470-114">Route templates</span></span>

<span data-ttu-id="ef470-115">El componente `Router` permite el enrutamiento a cada componente con una ruta especificada.</span><span class="sxs-lookup"><span data-stu-id="ef470-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="ef470-116">El componente `Router` aparece en el archivo *App.razor*:</span><span class="sxs-lookup"><span data-stu-id="ef470-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="ef470-117">Cuando se compila un archivo *.razor* con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Components.RouteAttribute>, donde se especifica la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="ef470-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="ef470-118">En tiempo de ejecución, el componente `RouteView`:</span><span class="sxs-lookup"><span data-stu-id="ef470-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="ef470-119">Recibe el elemento `RouteData` de `Router` junto con los parámetros deseados.</span><span class="sxs-lookup"><span data-stu-id="ef470-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="ef470-120">Representa el componente especificado con su diseño (o un diseño predeterminado opcional) por medio de los parámetros especificados.</span><span class="sxs-lookup"><span data-stu-id="ef470-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="ef470-121">Opcionalmente, se puede especificar un parámetro `DefaultLayout` con una clase de diseño para usarlo con aquellos componentes que no tengan especificado un diseño.</span><span class="sxs-lookup"><span data-stu-id="ef470-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="ef470-122">Las plantillas de Blazor predeterminadas especifican el componente `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="ef470-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="ef470-123">*MainLayout.razor* está en la carpeta *Shared* del proyecto de plantilla.</span><span class="sxs-lookup"><span data-stu-id="ef470-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="ef470-124">Para más información sobre los diseños, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="ef470-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="ef470-125">Se pueden aplicar varias plantillas de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="ef470-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ef470-126">El siguiente componente atiende las solicitudes de `/BlazorRoute` y `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="ef470-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="ef470-127">Para que las direcciones URL se resuelvan correctamente, la aplicación debe incluir una etiqueta `<base>` en su archivo *wwwroot/index.html* (WebAssembly de Blazor) o en su archivo *Pages/_Host.cshtml* (servidor Blazor) con la ruta de acceso base de la aplicación especificada en el atributo `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="ef470-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="ef470-128">Para obtener más información, vea <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="ef470-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="ef470-129">Suministro de contenido personalizado cuando no se encuentra contenido</span><span class="sxs-lookup"><span data-stu-id="ef470-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="ef470-130">El componente `Router` permite a la aplicación especificar contenido personalizado si no se encuentra contenido para la ruta solicitada.</span><span class="sxs-lookup"><span data-stu-id="ef470-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="ef470-131">En el archivo *App.razor*, establezca el contenido personalizado en el parámetro de plantilla `NotFound` del componente `Router`:</span><span class="sxs-lookup"><span data-stu-id="ef470-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="ef470-132">El contenido de las etiquetas `<NotFound>` puede incluir elementos arbitrarios, como otros componentes interactivos.</span><span class="sxs-lookup"><span data-stu-id="ef470-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="ef470-133">Para aplicar un diseño predeterminado al contenido de `NotFound`, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="ef470-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="ef470-134">Ruta a componentes desde varios ensamblados</span><span class="sxs-lookup"><span data-stu-id="ef470-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="ef470-135">Use el parámetro `AdditionalAssemblies` para especificar más ensamblados para que el componente `Router` los tenga en cuenta al buscar componentes enrutables.</span><span class="sxs-lookup"><span data-stu-id="ef470-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="ef470-136">Los ensamblados especificados se tendrán en cuenta además del ensamblado especificado por `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="ef470-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="ef470-137">En el siguiente ejemplo, `Component1` es un componente enrutable definido en una biblioteca de clases a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="ef470-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="ef470-138">El siguiente ejemplo de `AdditionalAssemblies` da como resultado una compatibilidad de enrutamiento con `Component1`:</span><span class="sxs-lookup"><span data-stu-id="ef470-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="ef470-139">Parámetros de ruta</span><span class="sxs-lookup"><span data-stu-id="ef470-139">Route parameters</span></span>

<span data-ttu-id="ef470-140">El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre (sin distinguir mayúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="ef470-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="ef470-141">No se admiten parámetros opcionales.</span><span class="sxs-lookup"><span data-stu-id="ef470-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="ef470-142">En el ejemplo anterior se aplican dos directivas `@page`.</span><span class="sxs-lookup"><span data-stu-id="ef470-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="ef470-143">La primera permite navegar al componente sin un parámetro,</span><span class="sxs-lookup"><span data-stu-id="ef470-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ef470-144">mientras que la segunda `@page` toma el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.</span><span class="sxs-lookup"><span data-stu-id="ef470-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="ef470-145">Restricciones de ruta</span><span class="sxs-lookup"><span data-stu-id="ef470-145">Route constraints</span></span>

<span data-ttu-id="ef470-146">Una restricción de ruta fuerza la coincidencia de tipos en un segmento de ruta a un componente.</span><span class="sxs-lookup"><span data-stu-id="ef470-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="ef470-147">En el siguiente ejemplo, la ruta al componente `Users` solo coincide en estos casos:</span><span class="sxs-lookup"><span data-stu-id="ef470-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="ef470-148">Existe un segmento de ruta `Id` en la dirección URL de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="ef470-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="ef470-149">El segmento `Id` es un entero (`int`).</span><span class="sxs-lookup"><span data-stu-id="ef470-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="ef470-150">En la siguiente tabla figuran las restricciones de ruta que hay disponibles.</span><span class="sxs-lookup"><span data-stu-id="ef470-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="ef470-151">Para más información sobre las restricciones de ruta que coinciden con la referencia cultural invariable, vea la advertencia que aparece después de la tabla.</span><span class="sxs-lookup"><span data-stu-id="ef470-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="ef470-152">Restricción</span><span class="sxs-lookup"><span data-stu-id="ef470-152">Constraint</span></span> | <span data-ttu-id="ef470-153">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="ef470-153">Example</span></span>           | <span data-ttu-id="ef470-154">Coincidencias de ejemplo</span><span class="sxs-lookup"><span data-stu-id="ef470-154">Example Matches</span></span>                                                                  | <span data-ttu-id="ef470-155">Invariable</span><span class="sxs-lookup"><span data-stu-id="ef470-155">Invariant</span></span><br><span data-ttu-id="ef470-156">referencia cultural</span><span class="sxs-lookup"><span data-stu-id="ef470-156">culture</span></span><br><span data-ttu-id="ef470-157">coincidencia</span><span class="sxs-lookup"><span data-stu-id="ef470-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="ef470-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="ef470-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="ef470-159">No</span><span class="sxs-lookup"><span data-stu-id="ef470-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="ef470-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="ef470-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="ef470-161">Sí</span><span class="sxs-lookup"><span data-stu-id="ef470-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="ef470-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="ef470-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="ef470-163">Sí</span><span class="sxs-lookup"><span data-stu-id="ef470-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="ef470-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ef470-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ef470-165">Sí</span><span class="sxs-lookup"><span data-stu-id="ef470-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="ef470-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="ef470-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="ef470-167">Sí</span><span class="sxs-lookup"><span data-stu-id="ef470-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="ef470-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="ef470-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="ef470-169">No</span><span class="sxs-lookup"><span data-stu-id="ef470-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="ef470-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ef470-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ef470-171">Sí</span><span class="sxs-lookup"><span data-stu-id="ef470-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="ef470-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="ef470-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="ef470-173">Sí</span><span class="sxs-lookup"><span data-stu-id="ef470-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="ef470-174">Las restricciones de ruta que comprueban la dirección URL y que se convierten en un tipo CLR (como `int` o `DateTime`) usan siempre la referencia cultural invariable.</span><span class="sxs-lookup"><span data-stu-id="ef470-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="ef470-175">Estas restricciones dan por supuesto que la dirección URL no es localizable.</span><span class="sxs-lookup"><span data-stu-id="ef470-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="ef470-176">Enrutamiento con direcciones URL que contienen puntos</span><span class="sxs-lookup"><span data-stu-id="ef470-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="ef470-177">En las aplicaciones de servidor Blazor, la ruta predeterminada en *_Host.cshtml* es `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="ef470-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="ef470-178">Una dirección URL de solicitud que contiene un punto (`.`) no coincide con la ruta predeterminada porque la dirección URL parece que solicita un archivo.</span><span class="sxs-lookup"><span data-stu-id="ef470-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="ef470-179">Una aplicación Blazor devuelve una respuesta *404 No encontrado* cuando un archivo estático no existe.</span><span class="sxs-lookup"><span data-stu-id="ef470-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="ef470-180">Para usar rutas que contienen un punto, configure *_Host.cshtml* con la siguiente plantilla de ruta:</span><span class="sxs-lookup"><span data-stu-id="ef470-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="ef470-181">La plantilla `"/{**path}"` incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ef470-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="ef470-182">Una sintaxis de *captura general* de doble asterisco (`**`) para capturar la ruta de acceso en varios límites de carpeta sin codificar las barras diagonales (`/`)</span><span class="sxs-lookup"><span data-stu-id="ef470-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="ef470-183">El nombre del parámetro de ruta `path`</span><span class="sxs-lookup"><span data-stu-id="ef470-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="ef470-184">La sintaxis de parámetros de *captura general* (`*`/`**`) **no** se admite en los componentes de Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="ef470-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="ef470-185">Para obtener más información, vea <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="ef470-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="ef470-186">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="ef470-186">NavLink component</span></span>

<span data-ttu-id="ef470-187">Use un componente `NavLink` en lugar de los elementos de hipervínculo HTML (`<a>`) cuando cree vínculos de navegación.</span><span class="sxs-lookup"><span data-stu-id="ef470-187">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="ef470-188">Un componente `NavLink` se comporta igual que un elemento `<a>`, salvo que alterna una clase CSS `active` en función de si su elemento `href` coincide con la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="ef470-188">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="ef470-189">La clase `active` ayuda a un usuario a entender qué página es la página activa entre los vínculos de navegación mostrados.</span><span class="sxs-lookup"><span data-stu-id="ef470-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="ef470-190">El siguiente componente `NavMenu` crea una barra de navegación de [Bootstrap](https://getbootstrap.com/docs/) que muestra cómo usar componentes `NavLink`:</span><span class="sxs-lookup"><span data-stu-id="ef470-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="ef470-191">Hay dos opciones de `NavLinkMatch` que se pueden asignar al atributo `Match` del elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="ef470-191">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="ef470-192">`NavLinkMatch.All`: el elemento `NavLink` estará activo cuando coincida con la dirección URL actual completa.</span><span class="sxs-lookup"><span data-stu-id="ef470-192">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="ef470-193">`NavLinkMatch.Prefix` (*predeterminado*): el elemento `NavLink` estará activo cuando coincida con cualquier prefijo de la dirección URL actual.</span><span class="sxs-lookup"><span data-stu-id="ef470-193">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="ef470-194">En el ejemplo anterior, el valor de `href=""` del elemento `NavLink` Home coincide con la dirección URL de inicio y solo recibe la clase CSS `active` en la dirección URL de la ruta de acceso base predeterminada de la aplicación (por ejemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="ef470-194">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="ef470-195">El segundo elemento `NavLink` recibe la clase `active` cuando el usuario visita una dirección URL con un prefijo `MyComponent` (por ejemplo, `https://localhost:5001/MyComponent` y `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="ef470-195">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="ef470-196">Se pasan más atributos del componente `NavLink` a la etiqueta delimitadora representada.</span><span class="sxs-lookup"><span data-stu-id="ef470-196">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="ef470-197">En el siguiente ejemplo, el componente `NavLink` incluye el atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="ef470-197">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="ef470-198">Se representa el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="ef470-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="ef470-199">Aplicaciones auxiliares de URI y estado de navegación</span><span class="sxs-lookup"><span data-stu-id="ef470-199">URI and navigation state helpers</span></span>

<span data-ttu-id="ef470-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabajar con los URI y con la navegación en el código de C#.</span><span class="sxs-lookup"><span data-stu-id="ef470-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="ef470-201">`NavigationManager` proporciona el evento y los métodos que se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="ef470-201">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="ef470-202">Miembro</span><span class="sxs-lookup"><span data-stu-id="ef470-202">Member</span></span> | <span data-ttu-id="ef470-203">Descripción</span><span class="sxs-lookup"><span data-stu-id="ef470-203">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="ef470-204">URI</span><span class="sxs-lookup"><span data-stu-id="ef470-204">Uri</span></span> | <span data-ttu-id="ef470-205">Obtiene el URI absoluto actual.</span><span class="sxs-lookup"><span data-stu-id="ef470-205">Gets the current absolute URI.</span></span> |
| <span data-ttu-id="ef470-206">BaseUri</span><span class="sxs-lookup"><span data-stu-id="ef470-206">BaseUri</span></span> | <span data-ttu-id="ef470-207">Obtiene el URI base (con una barra diagonal final) que se puede anteponer a las rutas de acceso de URI relativo para generar un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="ef470-207">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="ef470-208">Normalmente, `BaseUri` corresponde al atributo `href` del elemento `<base>` del documento en *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor).</span><span class="sxs-lookup"><span data-stu-id="ef470-208">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <span data-ttu-id="ef470-209">NavigateTo</span><span class="sxs-lookup"><span data-stu-id="ef470-209">NavigateTo</span></span> | <span data-ttu-id="ef470-210">Navega al URI especificado.</span><span class="sxs-lookup"><span data-stu-id="ef470-210">Navigates to the specified URI.</span></span> <span data-ttu-id="ef470-211">Si `forceLoad` es `true`:</span><span class="sxs-lookup"><span data-stu-id="ef470-211">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="ef470-212">El enrutamiento del lado cliente se omite.</span><span class="sxs-lookup"><span data-stu-id="ef470-212">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="ef470-213">Se fuerza al explorador a cargar la nueva página desde el servidor, tanto si el URI suele estar controlado por el enrutador del lado cliente como si no.</span><span class="sxs-lookup"><span data-stu-id="ef470-213">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <span data-ttu-id="ef470-214">LocationChanged</span><span class="sxs-lookup"><span data-stu-id="ef470-214">LocationChanged</span></span> | <span data-ttu-id="ef470-215">Evento que se desencadena cuando la ubicación de navegación ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="ef470-215">An event that fires when the navigation location has changed.</span></span> |
| <span data-ttu-id="ef470-216">ToAbsoluteUri</span><span class="sxs-lookup"><span data-stu-id="ef470-216">ToAbsoluteUri</span></span> | <span data-ttu-id="ef470-217">Convierte un URI relativo en un URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="ef470-217">Converts a relative URI into an absolute URI.</span></span> |
| <span data-ttu-id="ef470-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span><span class="sxs-lookup"><span data-stu-id="ef470-218"><span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span></span></span> | <span data-ttu-id="ef470-219">Dado un URI base (por ejemplo, un URI previamente devuelto por `GetBaseUri`), convierte un URI absoluto en un URI relativo al prefijo de URI base.</span><span class="sxs-lookup"><span data-stu-id="ef470-219">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="ef470-220">El siguiente componente navega al componente `Counter` de la aplicación cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="ef470-220">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="ef470-221">El componente siguiente controla un evento de cambio de ubicación.</span><span class="sxs-lookup"><span data-stu-id="ef470-221">The following component handles a location changed event.</span></span> <span data-ttu-id="ef470-222">El método `HandleLocationChanged` se desenlaza cuando el marco llama a `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="ef470-222">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="ef470-223">Al desenlazar el método se permite la recolección de elementos no utilizados del componente.</span><span class="sxs-lookup"><span data-stu-id="ef470-223">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="ef470-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> proporciona la información siguiente sobre el evento:</span><span class="sxs-lookup"><span data-stu-id="ef470-224"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="ef470-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: la dirección URL de la nueva ubicación.</span><span class="sxs-lookup"><span data-stu-id="ef470-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="ef470-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: si es `true`, Blazor ha interceptado la navegación del explorador.</span><span class="sxs-lookup"><span data-stu-id="ef470-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="ef470-227">Si es `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ha provocado que se produjera la navegación.</span><span class="sxs-lookup"><span data-stu-id="ef470-227">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="ef470-228">Para obtener más información sobre la eliminación de componentes, vea <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="ef470-228">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
