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
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor procedimientos recomendados de rendimiento de Webassembly

Por [Pranav Krishnamoorthy](https://github.com/pranavkm)

En este artículo se proporcionan instrucciones para ASP.NET Core Blazor procedimientos recomendados de rendimiento de Webassembly.

## <a name="avoid-unnecessary-component-renders"></a>Evitar representaciones de componentes innecesarias

Blazorel algoritmo de diferenciación de evita que se represente un componente cuando el algoritmo percibe que el componente no ha cambiado. Invalide [ComponentBase. ShouldRender](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A) para obtener un mayor control sobre la representación de componentes.

Si crea un componente de solo interfaz de usuario que nunca cambia después de la representación inicial, configure `ShouldRender` para que devuelva `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

La mayoría de las aplicaciones no requieren un control específico, sino <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> que también se pueden usar para representar de manera selectiva un componente que responde a un evento de interfaz de usuario.

En el ejemplo siguiente:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>se invalida y se establece en el valor del `shouldRender` campo, que es inicialmente `false` cuando se carga el componente.
* Cuando se selecciona el botón, `shouldRender` se establece en `true` , lo que obliga al componente a representarse con el actualizado `currentCount` .
* Inmediatamente después de la rerepresentación, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> establece el valor de en `shouldRender` `false` para evitar una posterior rerepresentación hasta la próxima vez que se seleccione el botón.

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

Para obtener más información, vea <xref:blazor/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualizar fragmentos que se pueden volver a usar

Los componentes ofrecen un enfoque práctico para generar fragmentos reutilizables de código y marcado. En general, se recomienda la creación de componentes individuales que se ajusten mejor a los requisitos de la aplicación. Una advertencia es que cada componente secundario adicional contribuye al tiempo total necesario para representar un componente primario. Para la mayoría de las aplicaciones, la sobrecarga adicional es insignificante. Las aplicaciones que generan un gran número de componentes deben considerar el uso de estrategias para reducir la sobrecarga de procesamiento, como limitar el número de componentes representados.

Por ejemplo, una cuadrícula o una lista que representa cientos de filas que contienen componentes es un uso intensivo del procesador para su representación. Considere la posibilidad de virtualizar un diseño de lista o cuadrícula para que solo se represente un subconjunto de los componentes en un momento dado. Para obtener un ejemplo de la representación de un subconjunto de componentes, consulte los siguientes componentes en la [aplicación de ejemplo de virtualización (repositorio de github de ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`componente ([Shared/virtualizate. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): componente escrito en C# que implementa <xref:Microsoft.AspNetCore.Components.ComponentBase> para representar un conjunto de filas de datos meteorológicos según el desplazamiento del usuario.
* `FetchData`componente ([pages/FetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): usa el `Virtualize` componente para mostrar 25 filas de datos meteorológicos cada vez.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Evitar la interoperabilidad de JavaScript para calcular las referencias de datos

En Blazor Webassembly, una llamada de interoperabilidad de JavaScript (JS) debe atravesar el límite Webassembly-JS. Al serializar y deserializar el contenido en los dos contextos, se crea una sobrecarga de procesamiento para la aplicación. Las llamadas de interoperabilidad de JS frecuentes suelen afectar negativamente al rendimiento. Para reducir la serialización de datos en el límite, determine si la aplicación puede consolidar muchas cargas pequeñas en una sola carga grande para evitar el gran volumen de cambios de contexto entre webassembly y JS.

## <a name="use-systemtextjson"></a>Uso de System. Text. JSON

Blazorla implementación de interoperabilidad de JS se basa en <xref:System.Text.Json> , que es una biblioteca de serialización de JSON de alto rendimiento con poca asignación de memoria. <xref:System.Text.Json>El uso de no produce un mayor tamaño de carga de aplicación sobre la adición de una o varias bibliotecas JSON alternativas.

Para obtener instrucciones sobre la migración, consulte [How to Migrate from Newtonsoft. JSON to System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Usar API de interoperabilidad de JS sincrónicas y no Marshall cuando sea necesario

BlazorWebassembly ofrece dos versiones adicionales de <xref:Microsoft.JSInterop.IJSRuntime> sobre la versión única disponible para las Blazor aplicaciones de servidor:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>permite invocar las llamadas de interoperabilidad de JS sincrónicamente, lo que tiene menos sobrecarga que las versiones asincrónicas:

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>permite llamadas de interoperabilidad de JS no Marshall:

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
  > Aunque el uso <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> de tiene la menor sobrecarga de los enfoques de interoperabilidad de JS, las API de JavaScript necesarias para interactuar con estas API actualmente no están documentadas y están sujetas a cambios importantes en las versiones futuras.

## <a name="reduce-app-size"></a>Reducir el tamaño de la aplicación

### <a name="intermediate-language-il-linking"></a>Vinculación de lenguaje intermedio (IL)

[Vincular un Blazor Aplicación webassembly](xref:host-and-deploy/blazor/configure-linker) reduce el tamaño de la aplicación recortando el código no utilizado en los archivos binarios de la aplicación. De forma predeterminada, el vinculador solo se habilita al compilar en la `Release` configuración. Para beneficiarse de esto, publique la aplicación para su implementación con el comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) con la opción [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) establecida en `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a>Deshabilitar características no utilizadas

BlazorEl tiempo de ejecución de webassembly incluye las siguientes características de .NET que se pueden deshabilitar si la aplicación no las requiere para un tamaño de carga más pequeño:

* Se incluye un archivo de datos para que la información de zona horaria sea correcta. Si la aplicación no requiere esta característica, considere la posibilidad de deshabilitarla estableciendo la `BlazorEnableTimeZoneSupport` propiedad de MSBuild en el archivo de proyecto de la aplicación en `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* La información de intercalación se incluye para que las API como <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> funcionen correctamente. Si está seguro de que la aplicación no necesita los datos de intercalación, considere la posibilidad de deshabilitarlo estableciendo la `BlazorWebAssemblyPreserveCollationData` propiedad de MSBuild en el archivo de proyecto de la aplicación para `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
