---
title: Control de eventos de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: aa338bbe61eec14bc1e1b3606e11e26bfb0e6a09
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967472"
---
# <a name="aspnet-core-blazor-event-handling"></a>Control de eventos de Blazor en ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

Los componentes de Razor proporcionan características de control de eventos. Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.

El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>. No es necesario llamar a [StateHasChanged](xref:blazor/lifecycle#state-changes) de forma manual. Las excepciones se registran cuando se producen.

En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a>Tipos de argumento de evento

En algunos eventos, se permiten los tipos de argumento de evento. Solo es necesario especificar un tipo de evento en la llamada de método si el tipo de evento se usa en el método.

En la tabla siguiente se muestran los valores `EventArgs` admitidos.

| evento            | Clase                | Eventos y notas de DOM |
| ---------------- | -------------------- | -------------------- |
| Portapapeles        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Arrastrar             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` y `DataTransferItem` contienen datos de elementos arrastrados. |
| Error            | `ErrorEventArgs`     | `onerror` |
| evento            | `EventArgs`          | *General*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Portapapeles*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrada*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Elementos multimedia*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Foco            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>No incluye compatibilidad con `relatedTarget`. |
| Entrada            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Teclado         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Puntero del mouse    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Rueda del mouse      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Progreso         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Entrada táctil            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` representa un único punto de contacto en un dispositivo sensible al tacto. |

Para obtener más información, vea los siguientes recursos:

* [Clases EventArgs en el origen de referencia de ASP.NET Core (dotnet/versión de aspnetcore/rama 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) incluye información sobre qué elementos HTML admite cada evento DOM.

## <a name="lambda-expressions"></a>Expresiones lambda

También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos. En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (`MouseEventArgs`) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> **No** use la variable de bucle (`i`) en un bucle `for` directamente en una expresión lambda. De lo contrario, todas las expresiones lambda usan la misma variable, lo que hace que el valor de `i` sea el mismo en todas las expresiones lambda. Capture siempre su valor en una variable local (`buttonNumber` en el ejemplo anterior) y, después, úsela.

## <a name="eventcallback"></a>EventCallback

Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de componente secundario, por ejemplo, cuando se produce un evento `onclick` en el elemento secundario. Para exponer eventos entre componentes, use un elemento `EventCallback`. Un componente primario puede asignar un método de devolución de llamada al elemento `EventCallback` de un componente secundario.

El elemento `ChildComponent` de la aplicación de ejemplo (*Components/ChildComponent.razor*) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de `EventCallback` del objeto `ParentComponent` del ejemplo. El elemento `EventCallback` tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` establece el objeto `EventCallback<T>` (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.

*Pages/ParentComponent.razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Cuando el botón se selecciona en `ChildComponent`:

* Se llama al método `ShowMessage` de `ParentComponent`. `messageText` se actualiza y se muestra en `ParentComponent`.
* No se requiere una llamada a [StateHasChanged](xref:blazor/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`). Se llama a `StateHasChanged` de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.

`EventCallback` y `EventCallback<T>` permiten delegados asincrónicos. `EventCallback<T>` está fuertemente tipado y requiere un tipo de argumento específico. `EventCallback` está débilmente tipado y permite cualquier tipo de argumento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Invoque `EventCallback` o `EventCallback<T>` con `InvokeAsync` y espere a <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Use `EventCallback` y `EventCallback<T>` para el control de eventos y el enlace de parámetros de componentes.

Se prefiere `EventCallback<T>` (fuertemente tipado) a `EventCallback`. `EventCallback<T>` proporciona mejores comentarios de errores a los usuarios del componente. Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional. Use `EventCallback` cuando no se pase ningún valor a la devolución de llamada.

## <a name="prevent-default-actions"></a>Bloqueo de acciones predeterminadas

Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.

Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto. En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`. El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.

El valor del atributo también puede ser una expresión. En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

No se necesita un controlador de eventos para impedir la acción predeterminada. El controlador de eventos y el bloqueo de escenarios de acción predeterminados se pueden usar de forma independiente.

## <a name="stop-event-propagation"></a>Detención de la propagación de eventos

Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.

En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
