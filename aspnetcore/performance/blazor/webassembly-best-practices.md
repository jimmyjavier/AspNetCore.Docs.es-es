---
title: ASP.NET Core Blazor procedimientos recomendados de rendimiento de Webassembly
author: pranavkm
description: Sugerencias para aumentar el rendimiento en ASP.NET Core Blazor aplicaciones Webassembly y evitar problemas de rendimiento comunes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 9e9b166cb9ce9870a8ff275b72bb12f04b84751b
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2020
ms.locfileid: "83439426"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="d188d-103">ASP.NET Core Blazor procedimientos recomendados de rendimiento de Webassembly</span><span class="sxs-lookup"><span data-stu-id="d188d-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="d188d-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="d188d-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="d188d-105">En este artículo se proporcionan instrucciones para ASP.NET Core Blazor procedimientos recomendados de rendimiento de Webassembly.</span><span class="sxs-lookup"><span data-stu-id="d188d-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="d188d-106">Evitar representaciones de componentes innecesarias</span><span class="sxs-lookup"><span data-stu-id="d188d-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="d188d-107">el algoritmo de diferenciación de evita que se represente un componente cuando el algoritmo percibe que el componente no ha cambiado.</span><span class="sxs-lookup"><span data-stu-id="d188d-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="d188d-108">Invalide [ComponentBase. ShouldRender](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A) para obtener un mayor control sobre la representación de componentes.</span><span class="sxs-lookup"><span data-stu-id="d188d-108">Override [ComponentBase.ShouldRender](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A) for fine-grained control over component rendering.</span></span>

<span data-ttu-id="d188d-109">Si crea un componente de solo interfaz de usuario que nunca cambia después de la representación inicial, configure `ShouldRender` para que devuelva `false` :</span><span class="sxs-lookup"><span data-stu-id="d188d-109">If authoring a UI-only component that never changes after the initial render, configure `ShouldRender` to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="d188d-110">La mayoría de las aplicaciones no requieren un control específico, sino <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> que también se pueden usar para representar de manera selectiva un componente que responde a un evento de interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="d188d-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="d188d-111">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="d188d-111">In the following example:</span></span>

* <span data-ttu-id="d188d-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>se invalida y se establece en el valor del `shouldRender` campo, que es inicialmente `false` cuando se carga el componente.</span><span class="sxs-lookup"><span data-stu-id="d188d-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the `shouldRender` field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="d188d-113">Cuando se selecciona el botón, `shouldRender` se establece en `true` , lo que obliga al componente a representarse con el actualizado `currentCount` .</span><span class="sxs-lookup"><span data-stu-id="d188d-113">When the button is selected, `shouldRender` is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="d188d-114">Inmediatamente después de la rerepresentación, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> establece el valor de en `shouldRender` `false` para evitar una posterior rerepresentación hasta la próxima vez que se seleccione el botón.</span><span class="sxs-lookup"><span data-stu-id="d188d-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of `shouldRender` back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="d188d-115">Para obtener más información, vea <xref:blazor/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="d188d-115">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="d188d-116">Virtualizar fragmentos que se pueden volver a usar</span><span class="sxs-lookup"><span data-stu-id="d188d-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="d188d-117">Los componentes ofrecen un enfoque práctico para generar fragmentos reutilizables de código y marcado.</span><span class="sxs-lookup"><span data-stu-id="d188d-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="d188d-118">En general, se recomienda la creación de componentes individuales que se ajusten mejor a los requisitos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d188d-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="d188d-119">Una advertencia es que cada componente secundario adicional contribuye al tiempo total necesario para representar un componente primario.</span><span class="sxs-lookup"><span data-stu-id="d188d-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="d188d-120">Para la mayoría de las aplicaciones, la sobrecarga adicional es insignificante.</span><span class="sxs-lookup"><span data-stu-id="d188d-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="d188d-121">Las aplicaciones que generan un gran número de componentes deben considerar el uso de estrategias para reducir la sobrecarga de procesamiento, como limitar el número de componentes representados.</span><span class="sxs-lookup"><span data-stu-id="d188d-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="d188d-122">Por ejemplo, una cuadrícula o una lista que representa cientos de filas que contienen componentes es un uso intensivo del procesador para su representación.</span><span class="sxs-lookup"><span data-stu-id="d188d-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="d188d-123">Considere la posibilidad de virtualizar un diseño de lista o cuadrícula para que solo se represente un subconjunto de los componentes en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="d188d-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="d188d-124">Para obtener un ejemplo de la representación de un subconjunto de componentes, consulte los siguientes componentes en la [aplicación de ejemplo de virtualización (repositorio de github de ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="d188d-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="d188d-125">`Virtualize`componente ([Shared/virtualizate. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente escrito en C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para representar un conjunto de filas de datos meteorológicos según el desplazamiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="d188d-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="d188d-126">`FetchData`componente ([pages/FetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa el `Virtualize` componente para mostrar 25 filas de datos meteorológicos cada vez.</span><span class="sxs-lookup"><span data-stu-id="d188d-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="d188d-127">Evitar la interoperabilidad de JavaScript para calcular las referencias de datos</span><span class="sxs-lookup"><span data-stu-id="d188d-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="d188d-128">En Blazor Webassembly, una llamada de interoperabilidad de JavaScript (JS) debe atravesar el límite Webassembly-JS.</span><span class="sxs-lookup"><span data-stu-id="d188d-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="d188d-129">Al serializar y deserializar el contenido en los dos contextos, se crea una sobrecarga de procesamiento para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d188d-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="d188d-130">Las llamadas de interoperabilidad de JS frecuentes suelen afectar negativamente al rendimiento.</span><span class="sxs-lookup"><span data-stu-id="d188d-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="d188d-131">Para reducir la serialización de datos en el límite, determine si la aplicación puede consolidar muchas cargas pequeñas en una sola carga grande para evitar el gran volumen de cambios de contexto entre webassembly y JS.</span><span class="sxs-lookup"><span data-stu-id="d188d-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="d188d-132">Uso de System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="d188d-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="d188d-133">la implementación de interoperabilidad de JS se basa en <xref:System.Text.Json> , que es una biblioteca de serialización de JSON de alto rendimiento con poca asignación de memoria.</span><span class="sxs-lookup"><span data-stu-id="d188d-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="d188d-134"><xref:System.Text.Json>El uso de no produce un mayor tamaño de carga de aplicación sobre la adición de una o varias bibliotecas JSON alternativas.</span><span class="sxs-lookup"><span data-stu-id="d188d-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="d188d-135">Para obtener instrucciones sobre la migración, consulte [How to Migrate from Newtonsoft. JSON to System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="d188d-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="d188d-136">Usar API de interoperabilidad de JS sincrónicas y no Marshall cuando sea necesario</span><span class="sxs-lookup"><span data-stu-id="d188d-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="d188d-137">Webassembly ofrece dos versiones adicionales de <xref:Microsoft.JSInterop.IJSRuntime> sobre la versión única disponible para las Blazor aplicaciones de servidor:</span><span class="sxs-lookup"><span data-stu-id="d188d-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="d188d-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime>permite invocar las llamadas de interoperabilidad de JS sincrónicamente, lo que tiene menos sobrecarga que las versiones asincrónicas:</span><span class="sxs-lookup"><span data-stu-id="d188d-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="d188d-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>permite llamadas de interoperabilidad de JS no Marshall:</span><span class="sxs-lookup"><span data-stu-id="d188d-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="d188d-140">Aunque el uso <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> de tiene la menor sobrecarga de los enfoques de interoperabilidad de JS, las API de JavaScript necesarias para interactuar con estas API actualmente no están documentadas y están sujetas a cambios importantes en las versiones futuras.</span><span class="sxs-lookup"><span data-stu-id="d188d-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="d188d-141">Reducir el tamaño de la aplicación</span><span class="sxs-lookup"><span data-stu-id="d188d-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="d188d-142">Vinculación de lenguaje intermedio (IL)</span><span class="sxs-lookup"><span data-stu-id="d188d-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="d188d-143">[Vincular un Blazor Aplicación webassembly](xref:host-and-deploy/blazor/configure-linker) reduce el tamaño de la aplicación recortando el código no utilizado en los archivos binarios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d188d-143">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="d188d-144">De forma predeterminada, el vinculador solo se habilita al compilar en la `Release` configuración.</span><span class="sxs-lookup"><span data-stu-id="d188d-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="d188d-145">Para beneficiarse de esto, publique la aplicación para su implementación con el comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) con la opción [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release` :</span><span class="sxs-lookup"><span data-stu-id="d188d-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a><span data-ttu-id="d188d-146">Deshabilitar características no utilizadas</span><span class="sxs-lookup"><span data-stu-id="d188d-146">Disable unused features</span></span>

Blazor<span data-ttu-id="d188d-147">El tiempo de ejecución de webassembly incluye las siguientes características de .NET que se pueden deshabilitar si la aplicación no las requiere para un tamaño de carga más pequeño:</span><span class="sxs-lookup"><span data-stu-id="d188d-147"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="d188d-148">Se incluye un archivo de datos para que la información de zona horaria sea correcta.</span><span class="sxs-lookup"><span data-stu-id="d188d-148">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="d188d-149">Si la aplicación no requiere esta característica, considere la posibilidad de deshabilitarla estableciendo la `BlazorEnableTimeZoneSupport` propiedad de MSBuild en el archivo de proyecto de la aplicación en `false` :</span><span class="sxs-lookup"><span data-stu-id="d188d-149">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="d188d-150">La información de intercalación se incluye para que las API como <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="d188d-150">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="d188d-151">Si está seguro de que la aplicación no necesita los datos de intercalación, considere la posibilidad de deshabilitarlo estableciendo la `BlazorWebAssemblyPreserveCollationData` propiedad de MSBuild en el archivo de proyecto de la aplicación para `false` :</span><span class="sxs-lookup"><span data-stu-id="d188d-151">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
