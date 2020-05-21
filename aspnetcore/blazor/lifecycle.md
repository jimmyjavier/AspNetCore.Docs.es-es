---
title: Ciclo de vida de ASP.NET Core Blazor
author: guardrex
description: Aprenda a usar los métodos de ciclo de vida de los componentes de Razor en aplicaciones de Blazor de ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e4fcd86b6e6a84d9e34a83688f9fb80c6907e5f3
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438920"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="40d61-103">Ciclo de vida de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="40d61-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="40d61-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="40d61-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="40d61-105">El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="40d61-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="40d61-106">Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="40d61-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="40d61-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="40d61-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="40d61-108">Métodos de inicialización de componentes</span><span class="sxs-lookup"><span data-stu-id="40d61-108">Component initialization methods</span></span>

<span data-ttu-id="40d61-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="40d61-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="40d61-110">Utilice `OnInitializedAsync` cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.</span><span class="sxs-lookup"><span data-stu-id="40d61-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="40d61-111">En el caso de una operación sincrónica, invalide `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="40d61-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="40d61-112">Para realizar una operación asincrónica, invalide `OnInitializedAsync` y use la palabra clave `await` en la operación:</span><span class="sxs-lookup"><span data-stu-id="40d61-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="40d61-113">Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/hosting-model-configuration#render-mode) llaman a `OnInitializedAsync` **_dos veces_**:</span><span class="sxs-lookup"><span data-stu-id="40d61-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="40d61-114">Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="40d61-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="40d61-115">Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="40d61-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="40d61-116">Para evitar que el código de desarrollador en `OnInitializedAsync` se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="40d61-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="40d61-117">Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="40d61-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="40d61-118">Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.</span><span class="sxs-lookup"><span data-stu-id="40d61-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="40d61-119">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="40d61-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="40d61-120">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="40d61-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="40d61-121">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="40d61-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="40d61-122">Antes de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="40d61-122">Before parameters are set</span></span>

<span data-ttu-id="40d61-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación:</span><span class="sxs-lookup"><span data-stu-id="40d61-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="40d61-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contiene todo el conjunto de valores de parámetro cada vez que se llama a `SetParametersAsync`.</span><span class="sxs-lookup"><span data-stu-id="40d61-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="40d61-125">La implementación predeterminada de `SetParametersAsync` establece el valor de cada propiedad con el atributo `[Parameter]` o `[CascadingParameter]`, que tiene un valor correspondiente en `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="40d61-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="40d61-126">Los parámetros que no tienen un valor correspondiente en `ParameterView` se dejan sin cambios.</span><span class="sxs-lookup"><span data-stu-id="40d61-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="40d61-127">Si no se invoca `base.SetParametersAync`, el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="40d61-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="40d61-128">Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.</span><span class="sxs-lookup"><span data-stu-id="40d61-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="40d61-129">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="40d61-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="40d61-130">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="40d61-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="40d61-131">Después de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="40d61-131">After parameters are set</span></span>

<span data-ttu-id="40d61-132">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>:</span><span class="sxs-lookup"><span data-stu-id="40d61-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="40d61-133">Cuando el componente se inicializa y ha recibido su primer conjunto de parámetros de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="40d61-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="40d61-134">Cuando el componente primario vuelve a representarse y proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="40d61-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="40d61-135">Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.</span><span class="sxs-lookup"><span data-stu-id="40d61-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="40d61-136">Cualquier parámetro de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="40d61-136">Any complex-typed parameters.</span></span> <span data-ttu-id="40d61-137">El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.</span><span class="sxs-lookup"><span data-stu-id="40d61-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="40d61-138">El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="40d61-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="40d61-139">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="40d61-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="40d61-140">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="40d61-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="40d61-141">Después de representar el componente</span><span class="sxs-lookup"><span data-stu-id="40d61-141">After component render</span></span>

<span data-ttu-id="40d61-142">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> una vez que un componente haya terminado la representación.</span><span class="sxs-lookup"><span data-stu-id="40d61-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="40d61-143">En este momento, se rellenan las referencias a elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="40d61-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="40d61-144">Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.</span><span class="sxs-lookup"><span data-stu-id="40d61-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="40d61-145">El parámetro `firstRender` para `OnAfterRenderAsync` y `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="40d61-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="40d61-146">Se establece en `true` la primera vez que se representa la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="40d61-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="40d61-147">Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.</span><span class="sxs-lookup"><span data-stu-id="40d61-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="40d61-148">El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="40d61-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="40d61-149">Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde `OnAfterRenderAsync`, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa.</span><span class="sxs-lookup"><span data-stu-id="40d61-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="40d61-150">Esto se hace para evitar un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="40d61-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="40d61-151">Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.</span><span class="sxs-lookup"><span data-stu-id="40d61-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="40d61-152">*No se llama a `OnAfterRender` y `OnAfterRenderAsync` al representarse previamente en el servidor.*</span><span class="sxs-lookup"><span data-stu-id="40d61-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="40d61-153">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="40d61-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="40d61-154">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="40d61-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="40d61-155">Supresión de la actualización de la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="40d61-155">Suppress UI refreshing</span></span>

<span data-ttu-id="40d61-156">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir la actualización de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="40d61-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="40d61-157">Si la implementación devuelve `true`, la interfaz de usuario se actualiza:</span><span class="sxs-lookup"><span data-stu-id="40d61-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="40d61-158">Se llama a `ShouldRender` cada vez que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="40d61-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="40d61-159">Aunque se invalide `ShouldRender`, el componente siempre se representa inicialmente.</span><span class="sxs-lookup"><span data-stu-id="40d61-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="40d61-160">Para obtener más información, vea <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="40d61-160">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="40d61-161">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="40d61-161">State changes</span></span>

<span data-ttu-id="40d61-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifica al componente que su estado ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="40d61-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="40d61-163">Cuando es aplicable, la llamada a `StateHasChanged` hace que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="40d61-163">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="40d61-164">Control de acciones asincrónicas incompletas en la representación</span><span class="sxs-lookup"><span data-stu-id="40d61-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="40d61-165">Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente.</span><span class="sxs-lookup"><span data-stu-id="40d61-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="40d61-166">Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="40d61-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="40d61-167">Proporcione lógica de representación para confirmar que los objetos se inicializan.</span><span class="sxs-lookup"><span data-stu-id="40d61-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="40d61-168">Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.</span><span class="sxs-lookup"><span data-stu-id="40d61-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="40d61-169">En el componente `FetchData` de las plantillas de Blazor, `OnInitializedAsync` se invalida para recibir de forma asincrónica datos de previsión (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="40d61-169">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="40d61-170">Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga.</span><span class="sxs-lookup"><span data-stu-id="40d61-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="40d61-171">Una vez que se completa el elemento `Task` que devuelve `OnInitializedAsync`, el componente se representará con el estado actualizado.</span><span class="sxs-lookup"><span data-stu-id="40d61-171">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="40d61-172">*Pages/FetchData.razor* en la plantilla de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="40d61-172">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="40d61-173">Eliminación de componentes con IDisposable</span><span class="sxs-lookup"><span data-stu-id="40d61-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="40d61-174">Si un componente implementa <xref:System.IDisposable>, se llama al [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="40d61-174">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="40d61-175">El componente siguiente utiliza `@implements IDisposable` y el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="40d61-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="40d61-176">No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="40d61-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="40d61-177">`StateHasChanged` se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.</span><span class="sxs-lookup"><span data-stu-id="40d61-177">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="40d61-178">Cancele la suscripción de los controladores de eventos de .NET.</span><span class="sxs-lookup"><span data-stu-id="40d61-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="40d61-179">En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="40d61-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="40d61-180">Enfoque de campo privado y expresión lambda</span><span class="sxs-lookup"><span data-stu-id="40d61-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="40d61-181">Enfoque de método privado</span><span class="sxs-lookup"><span data-stu-id="40d61-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="40d61-182">Control de errores</span><span class="sxs-lookup"><span data-stu-id="40d61-182">Handle errors</span></span>

<span data-ttu-id="40d61-183">Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="40d61-183">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="40d61-184">Reconexión con estado después de la representación previa</span><span class="sxs-lookup"><span data-stu-id="40d61-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="40d61-185">En una aplicación Blazor Server, cuando `RenderMode` es `ServerPrerendered`, el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="40d61-185">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="40d61-186">Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo.</span><span class="sxs-lookup"><span data-stu-id="40d61-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="40d61-187">Si el método de ciclo de vida [OnInitialized{Async}](#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="40d61-187">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="40d61-188">Cuando el componente se representa previamente de forma estática.</span><span class="sxs-lookup"><span data-stu-id="40d61-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="40d61-189">Después de establecerse la conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="40d61-189">After the server connection has been established.</span></span>

<span data-ttu-id="40d61-190">Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.</span><span class="sxs-lookup"><span data-stu-id="40d61-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="40d61-191">Para evitar el escenario de representación doble en una aplicación Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="40d61-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="40d61-192">Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="40d61-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="40d61-193">Use el identificador durante la representación previa para guardar el estado del componente.</span><span class="sxs-lookup"><span data-stu-id="40d61-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="40d61-194">Use el identificador después de la representación previa para recuperar el estado copiado en caché.</span><span class="sxs-lookup"><span data-stu-id="40d61-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="40d61-195">En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:</span><span class="sxs-lookup"><span data-stu-id="40d61-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="40d61-196">Para obtener más información sobre `RenderMode`, vea <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="40d61-196">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="40d61-197">Detección de cuándo se está obteniendo una representación previa de la aplicación</span><span class="sxs-lookup"><span data-stu-id="40d61-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="40d61-198">Trabajo en segundo plano cancelable</span><span class="sxs-lookup"><span data-stu-id="40d61-198">Cancelable background work</span></span>

<span data-ttu-id="40d61-199">Los componentes suelen llevar a cabo un trabajo en segundo plano de ejecución prolongada, como realizar llamadas de red (<xref:System.Net.Http.HttpClient>) e interactuar con bases de datos.</span><span class="sxs-lookup"><span data-stu-id="40d61-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="40d61-200">Es deseable detener el trabajo en segundo plano para conservar los recursos del sistema en diversas situaciones.</span><span class="sxs-lookup"><span data-stu-id="40d61-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="40d61-201">Por ejemplo, las operaciones asincrónicas en segundo plano no se detienen automáticamente cuando un usuario navega fuera de un componente.</span><span class="sxs-lookup"><span data-stu-id="40d61-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="40d61-202">Entre otras de las razones por las que los elementos de trabajo en segundo plano pueden requerir cancelación se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="40d61-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="40d61-203">Una tarea en segundo plano en ejecución se inició con datos de entrada o parámetros de procesamiento incorrectos.</span><span class="sxs-lookup"><span data-stu-id="40d61-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="40d61-204">El conjunto actual de elementos de trabajo en segundo plano en ejecución debe reemplazarse por un nuevo conjunto de elementos de trabajo.</span><span class="sxs-lookup"><span data-stu-id="40d61-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="40d61-205">La prioridad de las tareas que se están ejecutando debe cambiarse.</span><span class="sxs-lookup"><span data-stu-id="40d61-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="40d61-206">La aplicación debe apagarse para volver a implementarla en el servidor.</span><span class="sxs-lookup"><span data-stu-id="40d61-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="40d61-207">Los recursos del servidor se vuelven limitados, lo que requiere la reprogramación de los elementos de trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="40d61-207">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="40d61-208">Para implementar un patrón de trabajo en segundo plano cancelable en un componente:</span><span class="sxs-lookup"><span data-stu-id="40d61-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="40d61-209">Use <xref:System.Threading.CancellationTokenSource> y <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="40d61-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="40d61-210">Durante la [eliminación del componente](#component-disposal-with-idisposable), y en cualquier momento en que interese cancelar mediante la cancelación manual del token, llame a [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) para indicar que se debe cancelar el trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="40d61-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="40d61-211">Cuando se devuelva la llamada asincrónica, llame a <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> en el token.</span><span class="sxs-lookup"><span data-stu-id="40d61-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="40d61-212">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="40d61-212">In the following example:</span></span>

* <span data-ttu-id="40d61-213">`await Task.Delay(5000, cts.Token);` representa el trabajo asincrónico en segundo plano de ejecución prolongada.</span><span class="sxs-lookup"><span data-stu-id="40d61-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="40d61-214">`BackgroundResourceMethod` representa un método en segundo plano de ejecución prolongada que no debe iniciarse si se elimina `Resource` antes de llamar al método.</span><span class="sxs-lookup"><span data-stu-id="40d61-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
