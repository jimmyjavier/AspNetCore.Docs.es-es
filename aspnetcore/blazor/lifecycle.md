---
title: 'title: "Ciclo de vida de ASP.NET Core Blazor" author: description: "Aprenda a usar los métodos de ciclo de vida de los componentes de Razor en aplicaciones de Blazor de ASP.NET Core".'
author: guardrex
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
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
ms.openlocfilehash: 9dcbb2ca21cc689063198e1ccc90583db4229183
ms.sourcegitcommit: ea98ab8b2f61aa09f2d74edbb62db339fa06f105
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2020
ms.locfileid: "83864596"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="7fc16-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="7fc16-103">'Blazor'</span></span>

<span data-ttu-id="7fc16-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="7fc16-104">'Identity'</span></span>

<span data-ttu-id="7fc16-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="7fc16-105">'Let's Encrypt'</span></span> <span data-ttu-id="7fc16-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="7fc16-106">'Razor'</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="7fc16-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="7fc16-107">'SignalR' uid:</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="7fc16-108">Ciclo de vida de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7fc16-108">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="7fc16-109">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7fc16-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span> <span data-ttu-id="7fc16-110">El marco de Blazor incluye métodos de ciclo de vida sincrónicos y asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="7fc16-110">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span>

<span data-ttu-id="7fc16-111">Invalide los métodos de ciclo de vida para realizar operaciones adicionales en los componentes durante la inicialización y representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="7fc16-111">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="7fc16-112">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="7fc16-112">Lifecycle methods</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="7fc16-113">Métodos de inicialización de componentes</span><span class="sxs-lookup"><span data-stu-id="7fc16-113">Component initialization methods</span></span>

* <span data-ttu-id="7fc16-114"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> se invocan cuando se inicializa el componente después de haber recibido los parámetros iniciales de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="7fc16-114"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span>
* <span data-ttu-id="7fc16-115">Utilice <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> cuando el componente realice una operación asincrónica y deba actualizarse al completarse la operación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-115">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="7fc16-116">En el caso de una operación sincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="7fc16-116">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

<span data-ttu-id="7fc16-117">Para realizar una operación asincrónica, invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> y use el operador [await](/dotnet/csharp/language-reference/operators/await) en la operación:</span><span class="sxs-lookup"><span data-stu-id="7fc16-117">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [await](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span> <span data-ttu-id="7fc16-118">Las aplicaciones Blazor Server que [representan previamente su contenido](xref:blazor/hosting-model-configuration#render-mode) llaman a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dos veces_**:</span><span class="sxs-lookup"><span data-stu-id="7fc16-118">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span> <span data-ttu-id="7fc16-119">Una primera vez cuando el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="7fc16-119">Once when the component is initially rendered statically as part of the page.</span></span>

<span data-ttu-id="7fc16-120">Una segunda vez cuando el explorador establece una conexión de vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="7fc16-120">A second time when the browser establishes a connection back to the server.</span></span> <span data-ttu-id="7fc16-121">Para evitar que el código de desarrollador en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se ejecute dos veces, vea la sección [Reconexión con estado después de la representación previa](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="7fc16-121">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="7fc16-122">Durante la representación previa de una aplicación de Blazor Server, no es posible realizar ciertas acciones (como llamar a JavaScript), ya que no se ha establecido una conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="7fc16-122">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span>

<span data-ttu-id="7fc16-123">Es posible que los componentes tengan que representarse de forma diferente cuando se representen previamente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-123">Components may need to render differently when prerendered.</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="7fc16-124">Para obtener más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="7fc16-124">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="7fc16-125">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-125">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7fc16-126">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="7fc16-126">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

<span data-ttu-id="7fc16-127">Antes de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="7fc16-127">Before parameters are set</span></span> <span data-ttu-id="7fc16-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece los parámetros que proporciona el elemento primario del componente en el árbol de representación:</span><span class="sxs-lookup"><span data-stu-id="7fc16-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

<span data-ttu-id="7fc16-129"><xref:Microsoft.AspNetCore.Components.ParameterView> contiene todo el conjunto de valores de parámetro cada vez que se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-129"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span> <span data-ttu-id="7fc16-130">La implementación predeterminada de <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> establece el valor de cada propiedad con el atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) o [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), que tiene un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-130">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="7fc16-131">Los parámetros que no tienen un valor correspondiente en <xref:Microsoft.AspNetCore.Components.ParameterView> se dejan sin cambios.</span><span class="sxs-lookup"><span data-stu-id="7fc16-131">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="7fc16-132">Si no se invoca [base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A), el código personalizado puede interpretar el valor de los parámetros entrantes de cualquier manera que sea necesaria.</span><span class="sxs-lookup"><span data-stu-id="7fc16-132">If [base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span>

* <span data-ttu-id="7fc16-133">Por ejemplo, no hay ningún requisito para asignar los parámetros entrantes a las propiedades de la clase.</span><span class="sxs-lookup"><span data-stu-id="7fc16-133">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>
* <span data-ttu-id="7fc16-134">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-134">If any event handlers are set up, unhook them on disposal.</span></span>
  * <span data-ttu-id="7fc16-135">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="7fc16-135">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>
  * <span data-ttu-id="7fc16-136">Después de establecer los parámetros</span><span class="sxs-lookup"><span data-stu-id="7fc16-136">After parameters are set</span></span> <span data-ttu-id="7fc16-137">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A>:</span><span class="sxs-lookup"><span data-stu-id="7fc16-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="7fc16-138">Cuando el componente se inicializa y ha recibido su primer conjunto de parámetros de su componente primario.</span><span class="sxs-lookup"><span data-stu-id="7fc16-138">When the component is initialized and has received its first set of parameters from its parent component.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="7fc16-139">Cuando el componente primario vuelve a representarse y proporciona lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7fc16-139">When the parent component re-renders and supplies:</span></span> <span data-ttu-id="7fc16-140">Solo se conocen tipos inmutables primitivos, de los que se ha cambiado por lo menos un parámetro.</span><span class="sxs-lookup"><span data-stu-id="7fc16-140">Only known primitive immutable types of which at least one parameter has changed.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="7fc16-141">Cualquier parámetro de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="7fc16-141">Any complex-typed parameters.</span></span>

<span data-ttu-id="7fc16-142">El marco no puede saber si los valores de un parámetro de tipo complejo se han transformado internamente, por lo que trata el conjunto de parámetros como modificado.</span><span class="sxs-lookup"><span data-stu-id="7fc16-142">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span> <span data-ttu-id="7fc16-143">El trabajo asincrónico al aplicar parámetros y valores de propiedad debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-143">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span> <span data-ttu-id="7fc16-144">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-144">If any event handlers are set up, unhook them on disposal.</span></span>

<span data-ttu-id="7fc16-145">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="7fc16-145">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

* <span data-ttu-id="7fc16-146">Después de representar el componente</span><span class="sxs-lookup"><span data-stu-id="7fc16-146">After component render</span></span>
* <span data-ttu-id="7fc16-147">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> una vez que un componente haya terminado la representación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span>

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
> <span data-ttu-id="7fc16-148">En este momento, se rellenan las referencias a elementos y componentes.</span><span class="sxs-lookup"><span data-stu-id="7fc16-148">Element and component references are populated at this point.</span></span>
>
> <span data-ttu-id="7fc16-149">Use esta fase para realizar pasos de inicialización adicionales mediante el contenido representado, como la activación de bibliotecas de JavaScript de terceros que operan en los elementos DOM representados.</span><span class="sxs-lookup"><span data-stu-id="7fc16-149">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span> <span data-ttu-id="7fc16-150">El parámetro `firstRender` para <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="7fc16-150">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span> <span data-ttu-id="7fc16-151">Se establece en `true` la primera vez que se representa la instancia del componente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-151">Is set to `true` the first time that the component instance is rendered.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="7fc16-152">Se puede utilizar para garantizar que el trabajo de inicialización solo se realiza una vez.</span><span class="sxs-lookup"><span data-stu-id="7fc16-152">Can be used to ensure that initialization work is only performed once.</span></span>

<span data-ttu-id="7fc16-153">El trabajo asincrónico inmediatamente después de la representación debe producirse durante el evento de ciclo de vida de <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-153">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span> <span data-ttu-id="7fc16-154">Incluso si se devuelve un elemento <xref:System.Threading.Tasks.Task> desde <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, el marco no programa un ciclo de representación adicional para el componente una vez que la tarea se completa.</span><span class="sxs-lookup"><span data-stu-id="7fc16-154">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="7fc16-155">Esto se hace para evitar un bucle de representación infinito.</span><span class="sxs-lookup"><span data-stu-id="7fc16-155">This is to avoid an infinite render loop.</span></span>

<span data-ttu-id="7fc16-156">Es distinto del resto de métodos de ciclo de vida, los cuales programan un ciclo de representación adicional una vez se completa la tarea devuelta.</span><span class="sxs-lookup"><span data-stu-id="7fc16-156">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span> <span data-ttu-id="7fc16-157">*No se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> y <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> al representarse previamente en el servidor.*</span><span class="sxs-lookup"><span data-stu-id="7fc16-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="7fc16-158">Si hay algún controlador de eventos configurado, desenlácelo durante la eliminación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-158">If any event handlers are set up, unhook them on disposal.</span></span>

<span data-ttu-id="7fc16-159">Para obtener más información, vea la sección [Eliminación de componentes con IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="7fc16-159">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

<span data-ttu-id="7fc16-160">Supresión de la actualización de la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="7fc16-160">Suppress UI refreshing</span></span>

## <a name="state-changes"></a><span data-ttu-id="7fc16-161">Invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> para suprimir la actualización de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="7fc16-161">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span>

<span data-ttu-id="7fc16-162">Si la implementación devuelve `true`, la interfaz de usuario se actualiza:</span><span class="sxs-lookup"><span data-stu-id="7fc16-162">If the implementation returns `true`, the UI is refreshed:</span></span> <span data-ttu-id="7fc16-163">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> cada vez que se representa el componente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-163"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="7fc16-164">Aunque se invalide <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, el componente siempre se representa inicialmente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-164">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="7fc16-165">Para obtener más información, vea <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-165">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span> <span data-ttu-id="7fc16-166">Cambios de estado</span><span class="sxs-lookup"><span data-stu-id="7fc16-166">State changes</span></span> <span data-ttu-id="7fc16-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifica al componente que su estado ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="7fc16-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="7fc16-168">Cuando es aplicable, la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> hace que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-168">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="7fc16-169">Control de acciones asincrónicas incompletas en la representación</span><span class="sxs-lookup"><span data-stu-id="7fc16-169">Handle incomplete async actions at render</span></span> <span data-ttu-id="7fc16-170">Es posible que las acciones asincrónicas realizadas en eventos de ciclo de vida no se hayan completado antes de que se represente el componente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-170">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="7fc16-171">Los objetos podrían ser `null` o rellenarse de forma incompleta con datos mientras se ejecuta el método de ciclo de vida.</span><span class="sxs-lookup"><span data-stu-id="7fc16-171">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span>

<span data-ttu-id="7fc16-172">Proporcione lógica de representación para confirmar que los objetos se inicializan.</span><span class="sxs-lookup"><span data-stu-id="7fc16-172">Provide rendering logic to confirm that objects are initialized.</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="7fc16-173">Represente los elementos de la interfaz de usuario del marcador de posición (por ejemplo, un mensaje de carga) mientras los objetos sean `null`.</span><span class="sxs-lookup"><span data-stu-id="7fc16-173">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="7fc16-174">En el componente `FetchData` de las plantillas de Blazor, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> se invalida para recibir de forma asincrónica datos de previsión (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="7fc16-174">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="7fc16-175">Cuando `forecasts` es `null`, se muestra al usuario un mensaje de carga.</span><span class="sxs-lookup"><span data-stu-id="7fc16-175">When `forecasts` is `null`, a loading message is displayed to the user.</span></span>

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
> <span data-ttu-id="7fc16-176">Una vez que se completa el elemento `Task` que devuelve <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, el componente se representará con el estado actualizado.</span><span class="sxs-lookup"><span data-stu-id="7fc16-176">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span> <span data-ttu-id="7fc16-177">*Pages/FetchData.razor* en la plantilla de Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="7fc16-177">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

<span data-ttu-id="7fc16-178">Eliminación de componentes con IDisposable</span><span class="sxs-lookup"><span data-stu-id="7fc16-178">Component disposal with IDisposable</span></span> <span data-ttu-id="7fc16-179">Si un componente implementa <xref:System.IDisposable>, se llama al [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) cuando se quita el componente de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="7fc16-179">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span>

* <span data-ttu-id="7fc16-180">El componente siguiente utiliza `@implements IDisposable` y el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="7fc16-180">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="7fc16-181">No se admite la llamada a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> en `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="7fc16-181">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="7fc16-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se puede invocar como parte de la desactivación del representador, por lo que no se admite la solicitud de actualizaciones de la interfaz de usuario en ese momento.</span><span class="sxs-lookup"><span data-stu-id="7fc16-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="7fc16-183">Cancele la suscripción de los controladores de eventos de .NET.</span><span class="sxs-lookup"><span data-stu-id="7fc16-183">Unsubscribe event handlers from .NET events.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="7fc16-184">En los ejemplos de [formulario de Blazor](xref:blazor/forms-validation) siguientes se muestra cómo desenlazar un controlador de eventos en el método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="7fc16-184">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

<span data-ttu-id="7fc16-185">Enfoque de campo privado y expresión lambda</span><span class="sxs-lookup"><span data-stu-id="7fc16-185">Private field and lambda approach</span></span> <span data-ttu-id="7fc16-186">Enfoque de método privado</span><span class="sxs-lookup"><span data-stu-id="7fc16-186">Private method approach</span></span> <span data-ttu-id="7fc16-187">Control de errores</span><span class="sxs-lookup"><span data-stu-id="7fc16-187">Handle errors</span></span>

* <span data-ttu-id="7fc16-188">Para obtener información sobre cómo controlar los errores durante la ejecución del método de ciclo de vida, vea <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-188">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
* <span data-ttu-id="7fc16-189">Reconexión con estado después de la representación previa</span><span class="sxs-lookup"><span data-stu-id="7fc16-189">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="7fc16-190">En una aplicación Blazor Server, cuando <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> es <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, el componente se representa inicialmente de forma estática como parte de la página.</span><span class="sxs-lookup"><span data-stu-id="7fc16-190">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span>

<span data-ttu-id="7fc16-191">Una vez que el explorador vuelve a establecer una conexión con el servidor, el componente se representa *otra vez* y el componente ahora es interactivo.</span><span class="sxs-lookup"><span data-stu-id="7fc16-191">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span>

* <span data-ttu-id="7fc16-192">Si el método de ciclo de vida [OnInitialized{Async}](#component-initialization-methods) para inicializar el componente está presente, el método se ejecuta *dos veces*:</span><span class="sxs-lookup"><span data-stu-id="7fc16-192">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>
* <span data-ttu-id="7fc16-193">Cuando el componente se representa previamente de forma estática.</span><span class="sxs-lookup"><span data-stu-id="7fc16-193">When the component is prerendered statically.</span></span>
* <span data-ttu-id="7fc16-194">Después de establecerse la conexión con el servidor.</span><span class="sxs-lookup"><span data-stu-id="7fc16-194">After the server connection has been established.</span></span>

<span data-ttu-id="7fc16-195">Esto puede dar como resultado un cambio evidente en los datos mostrados en la interfaz de usuario cuando el componente se representa finalmente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-195">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

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

<span data-ttu-id="7fc16-196">Para evitar el escenario de representación doble en una aplicación Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="7fc16-196">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="7fc16-197">Pase un identificador que se pueda usar para copiar en caché el estado durante la representación previa y recuperar el estado después de reiniciarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7fc16-197">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="7fc16-198">Use el identificador durante la representación previa para guardar el estado del componente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-198">Use the identifier during prerendering to save component state.</span></span>

<span data-ttu-id="7fc16-199">Use el identificador después de la representación previa para recuperar el estado copiado en caché.</span><span class="sxs-lookup"><span data-stu-id="7fc16-199">Use the identifier after prerendering to retrieve the cached state.</span></span> <span data-ttu-id="7fc16-200">En el código siguiente se muestra un elemento `WeatherForecastService` actualizado en una aplicación Blazor Server basada en plantillas que evita la representación doble:</span><span class="sxs-lookup"><span data-stu-id="7fc16-200">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span> <span data-ttu-id="7fc16-201">Para obtener más información sobre <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, vea <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-201">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

<span data-ttu-id="7fc16-202">Detección de cuándo se está obteniendo una representación previa de la aplicación</span><span class="sxs-lookup"><span data-stu-id="7fc16-202">Detect when the app is prerendering</span></span>

* <span data-ttu-id="7fc16-203">Trabajo en segundo plano cancelable</span><span class="sxs-lookup"><span data-stu-id="7fc16-203">Cancelable background work</span></span>
* <span data-ttu-id="7fc16-204">Los componentes suelen llevar a cabo un trabajo en segundo plano de ejecución prolongada, como realizar llamadas de red (<xref:System.Net.Http.HttpClient>) e interactuar con bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7fc16-204">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span>
* <span data-ttu-id="7fc16-205">Es deseable detener el trabajo en segundo plano para conservar los recursos del sistema en diversas situaciones.</span><span class="sxs-lookup"><span data-stu-id="7fc16-205">It's desirable to stop the background work to conserve system resources in several situations.</span></span>
* <span data-ttu-id="7fc16-206">Por ejemplo, las operaciones asincrónicas en segundo plano no se detienen automáticamente cuando un usuario navega fuera de un componente.</span><span class="sxs-lookup"><span data-stu-id="7fc16-206">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>
* <span data-ttu-id="7fc16-207">Entre otras de las razones por las que los elementos de trabajo en segundo plano pueden requerir cancelación se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="7fc16-207">Other reasons why background work items might require cancellation include:</span></span>

<span data-ttu-id="7fc16-208">Una tarea en segundo plano en ejecución se inició con datos de entrada o parámetros de procesamiento incorrectos.</span><span class="sxs-lookup"><span data-stu-id="7fc16-208">An executing background task was started with faulty input data or processing parameters.</span></span>

* <span data-ttu-id="7fc16-209">El conjunto actual de elementos de trabajo en segundo plano en ejecución debe reemplazarse por un nuevo conjunto de elementos de trabajo.</span><span class="sxs-lookup"><span data-stu-id="7fc16-209">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="7fc16-210">La prioridad de las tareas que se están ejecutando debe cambiarse.</span><span class="sxs-lookup"><span data-stu-id="7fc16-210">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="7fc16-211">La aplicación debe apagarse para volver a implementarla en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7fc16-211">The app has to be shut down in order to redeploy it to the server.</span></span>

<span data-ttu-id="7fc16-212">Los recursos del servidor se vuelven limitados, lo que requiere la reprogramación de los elementos de trabajo en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="7fc16-212">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

* <span data-ttu-id="7fc16-213">Para implementar un patrón de trabajo en segundo plano cancelable en un componente:</span><span class="sxs-lookup"><span data-stu-id="7fc16-213">To implement a cancelable background work pattern in a component:</span></span>
* <span data-ttu-id="7fc16-214">Use <xref:System.Threading.CancellationTokenSource> y <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="7fc16-214">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>

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
