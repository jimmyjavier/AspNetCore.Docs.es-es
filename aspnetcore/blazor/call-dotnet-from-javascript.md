---
title: Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor
author: guardrex
description: Obtenga información sobre cómo invocar métodos de .NET desde funciones de JavaScript en aplicaciones de Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 31e72eeac415f10d573de455f19aa8ff34743356
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242411"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Llamada a métodos de .NET desde funciones de JavaScript en ASP.NET Core Blazor

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co) y [Luke Latham](https://github.com/guardrex)

Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa. Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).

En este artículo se describe cómo invocar métodos de .NET desde JavaScript. Para obtener información sobre cómo llamar a funciones de JavaScript desde .NET, vea <xref:blazor/call-javascript-from-dotnet>.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Llamada al método estático de .NET

Para invocar un método de .NET estático desde JavaScript, use las funciones `DotNet.invokeMethod` o `DotNet.invokeMethodAsync`. Pase el identificador del método estático al que quiere llamar, el nombre del ensamblado que contiene la función, y los argumentos. La versión asincrónica es preferible para admitir escenarios de Blazor Server. El método de .NET debe ser público y estático y debe tener el atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute). Actualmente no se admite la llamada a métodos genéricos abiertos.

La aplicación de ejemplo incluye un método de C# para devolver una matriz `int`. El atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) se aplica al método.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

El archivo JavaScript servido al cliente invoca el método de .NET de C#.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Al seleccionar el botón **`Trigger .NET static method ReturnArrayAsync`** , examine la salida de la consola en las herramientas de desarrollo web del explorador.

La salida de la consola es:

```console
Array(4) [ 1, 2, 3, 4 ]
```

El cuarto valor de matriz se inserta en la matriz (`data.push(4);`) devuelta por `ReturnArrayAsync`.

De forma predeterminada, el identificador del método es su nombre, pero puede especificar un identificador diferente mediante el constructor de atributo [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute):

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

En el archivo JavaScript del lado cliente:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Llamada a métodos de instancia

También puede llamar a métodos de instancia de .NET desde JavaScript. Para invocar un método de instancia de .NET desde JavaScript:

* Pase la instancia de .NET por referencia a JavaScript:
  * Realice una llamada estática a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.
  * Encapsule la instancia de en una instancia <xref:Microsoft.JSInterop.DotNetObjectReference> y llame a <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> en la instancia <xref:Microsoft.JSInterop.DotNetObjectReference>. Elimine los objetos <xref:Microsoft.JSInterop.DotNetObjectReference> (se incluye un ejemplo más adelante en esta sección).
* Invoque métodos de instancia de .NET en la instancia mediante las funciones `invokeMethod` o `invokeMethodAsync`. La instancia de .NET también se puede pasar como argumento al invocar otros métodos de .NET desde JavaScript.

> [!NOTE]
> La aplicación de ejemplo registra los mensajes en la consola del lado cliente. En los siguientes ejemplos de esta aplicación, examine la salida de la consola del explorador en las herramientas de desarrollo del explorador.

Al seleccionar el botón **`Trigger .NET instance method HelloHelper.SayHello`** , se llama a `ExampleJsInterop.CallHelloHelperSayHello` y pasa un nombre, `Blazor`, al método.

`Pages/JsInterop.razor`:

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` invoca la función `sayHello` de JavaScript con una nueva instancia de `HelloHelper`.

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

El nombre se pasa al constructor de `HelloHelper`, que establece la propiedad `HelloHelper.Name`. Cuando se ejecuta la función `sayHello` de JavaScript, `HelloHelper.SayHello` devuelve el mensaje `Hello, {Name}!`, y la función de JavaScript lo escribe en la consola.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Salida de la consola en las herramientas de desarrollo web del explorador:

```console
Hello, Blazor!
```

Para evitar una fuga de memoria y permitir la recolección de elementos no utilizados en un componente que crea un objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, adopte uno de los enfoques siguientes:

* Elimine el objeto de la clase que ha creado la instancia de <xref:Microsoft.JSInterop.DotNetObjectReference>:

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  El patrón anterior que se muestra en la clase `ExampleJsInterop` también se puede implementar en un componente:

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

* Si el componente o la clase no elimina el objeto <xref:Microsoft.JSInterop.DotNetObjectReference>, elimínelo en el cliente mediante la llamada a `.dispose()`:

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Llamada a métodos de instancia de componente

Para invocar los métodos de .NET de un componente:

* Use la función `invokeMethod` o `invokeMethodAsync` para hacer una llamada de método estático al componente.
* El método estático del componente encapsula la llamada a su método de instancia como un objeto <xref:System.Action> invocado.

En el código JavaScript del lado cliente:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

`Pages/JSInteropComponent.razor`:

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

Cuando hay varios componentes, cada uno con métodos de instancia a los que llamar, se usa una clase auxiliar para invocar los métodos de instancia (como objetos <xref:System.Action>) de cada componente.

En el ejemplo siguiente:

* El componente `JSInteropExample` contiene varios componentes `ListItem`.
* Cada componente `ListItem` consta de un mensaje y un botón.
* Cuando se selecciona un botón de componente `ListItem`, el método `UpdateMessage` de ese objeto `ListItem` cambia el texto del elemento de lista y oculta el botón.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

En el código JavaScript del lado cliente:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

`Shared/ListItem.razor`:

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

`Pages/JSInteropExample.razor`:

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Evitar referencias de objetos circulares

Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:

* Llamadas de método .NET.
* Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.

Para más información, consulte los problemas siguientes:

* [Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525
* [Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)

## <a name="additional-resources"></a>Recursos adicionales

* <xref:blazor/call-javascript-from-dotnet>
* [Ejemplo de `InteropComponent.razor` (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Realización de transferencias de gran cantidad de datos en aplicaciones de servidor Blazor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
