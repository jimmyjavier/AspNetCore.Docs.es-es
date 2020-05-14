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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="f4b8f-103">Control de eventos de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4b8f-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="f4b8f-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f4b8f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f4b8f-105">Los componentes de Razor proporcionan características de control de eventos.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-105">Razor components provide event handling features.</span></span> <span data-ttu-id="f4b8f-106">Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="f4b8f-107">El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="f4b8f-108">El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="f4b8f-109">Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="f4b8f-110">No es necesario llamar a [StateHasChanged](xref:blazor/lifecycle#state-changes) de forma manual.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="f4b8f-111">Las excepciones se registran cuando se producen.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="f4b8f-112">En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="f4b8f-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="f4b8f-113">Event argument types</span></span>

<span data-ttu-id="f4b8f-114">En algunos eventos, se permiten los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="f4b8f-115">Solo es necesario especificar un tipo de evento en la llamada de método si el tipo de evento se usa en el método.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="f4b8f-116">En la tabla siguiente se muestran los valores `EventArgs` admitidos.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="f4b8f-117">evento</span><span class="sxs-lookup"><span data-stu-id="f4b8f-117">Event</span></span>            | <span data-ttu-id="f4b8f-118">Clase</span><span class="sxs-lookup"><span data-stu-id="f4b8f-118">Class</span></span>                | <span data-ttu-id="f4b8f-119">Eventos y notas de DOM</span><span class="sxs-lookup"><span data-stu-id="f4b8f-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="f4b8f-120">Portapapeles</span><span class="sxs-lookup"><span data-stu-id="f4b8f-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="f4b8f-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="f4b8f-122">Arrastrar</span><span class="sxs-lookup"><span data-stu-id="f4b8f-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="f4b8f-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="f4b8f-124">`DataTransfer` y `DataTransferItem` contienen datos de elementos arrastrados.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="f4b8f-125">Error</span><span class="sxs-lookup"><span data-stu-id="f4b8f-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="f4b8f-126">evento</span><span class="sxs-lookup"><span data-stu-id="f4b8f-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="f4b8f-127">*General*</span><span class="sxs-lookup"><span data-stu-id="f4b8f-127">*General*</span></span><br><span data-ttu-id="f4b8f-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="f4b8f-129">*Portapapeles*</span><span class="sxs-lookup"><span data-stu-id="f4b8f-129">*Clipboard*</span></span><br><span data-ttu-id="f4b8f-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="f4b8f-131">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="f4b8f-131">*Input*</span></span><br><span data-ttu-id="f4b8f-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="f4b8f-133">*Elementos multimedia*</span><span class="sxs-lookup"><span data-stu-id="f4b8f-133">*Media*</span></span><br><span data-ttu-id="f4b8f-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="f4b8f-135">Foco</span><span class="sxs-lookup"><span data-stu-id="f4b8f-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="f4b8f-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="f4b8f-137">No incluye compatibilidad con `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="f4b8f-138">Entrada</span><span class="sxs-lookup"><span data-stu-id="f4b8f-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="f4b8f-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="f4b8f-140">Teclado</span><span class="sxs-lookup"><span data-stu-id="f4b8f-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="f4b8f-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="f4b8f-142">Mouse</span><span class="sxs-lookup"><span data-stu-id="f4b8f-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="f4b8f-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="f4b8f-144">Puntero del mouse</span><span class="sxs-lookup"><span data-stu-id="f4b8f-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="f4b8f-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="f4b8f-146">Rueda del mouse</span><span class="sxs-lookup"><span data-stu-id="f4b8f-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="f4b8f-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="f4b8f-148">Progreso</span><span class="sxs-lookup"><span data-stu-id="f4b8f-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="f4b8f-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="f4b8f-150">Entrada táctil</span><span class="sxs-lookup"><span data-stu-id="f4b8f-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="f4b8f-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="f4b8f-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="f4b8f-152">`TouchPoint` representa un único punto de contacto en un dispositivo sensible al tacto.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="f4b8f-153">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="f4b8f-154">[Clases EventArgs en el origen de referencia de ASP.NET Core (dotnet/versión de aspnetcore/rama 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="f4b8f-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="f4b8f-155">[Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) incluye información sobre qué elementos HTML admite cada evento DOM.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="f4b8f-156">Expresiones lambda</span><span class="sxs-lookup"><span data-stu-id="f4b8f-156">Lambda expressions</span></span>

<span data-ttu-id="f4b8f-157">También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="f4b8f-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="f4b8f-158">A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="f4b8f-159">En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (`MouseEventArgs`) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="f4b8f-160">**No** use la variable de bucle (`i`) en un bucle `for` directamente en una expresión lambda.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="f4b8f-161">De lo contrario, todas las expresiones lambda usan la misma variable, lo que hace que el valor de `i` sea el mismo en todas las expresiones lambda.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="f4b8f-162">Capture siempre su valor en una variable local (`buttonNumber` en el ejemplo anterior) y, después, úsela.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="f4b8f-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="f4b8f-163">EventCallback</span></span>

<span data-ttu-id="f4b8f-164">Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de componente secundario, por ejemplo, cuando se produce un evento `onclick` en el elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="f4b8f-165">Para exponer eventos entre componentes, use un elemento `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="f4b8f-166">Un componente primario puede asignar un método de devolución de llamada al elemento `EventCallback` de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="f4b8f-167">El elemento `ChildComponent` de la aplicación de ejemplo (*Components/ChildComponent.razor*) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de `EventCallback` del objeto `ParentComponent` del ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="f4b8f-168">El elemento `EventCallback` tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="f4b8f-169">`ParentComponent` establece el objeto `EventCallback<T>` (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="f4b8f-170">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="f4b8f-171">Cuando el botón se selecciona en `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="f4b8f-172">Se llama al método `ShowMessage` de `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="f4b8f-173">`messageText` se actualiza y se muestra en `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-173">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="f4b8f-174">No se requiere una llamada a [StateHasChanged](xref:blazor/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="f4b8f-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="f4b8f-175">Se llama a `StateHasChanged` de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="f4b8f-176">`EventCallback` y `EventCallback<T>` permiten delegados asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="f4b8f-177">`EventCallback<T>` está fuertemente tipado y requiere un tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="f4b8f-178">`EventCallback` está débilmente tipado y permite cualquier tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="f4b8f-179">Invoque `EventCallback` o `EventCallback<T>` con `InvokeAsync` y espere a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="f4b8f-180">Use `EventCallback` y `EventCallback<T>` para el control de eventos y el enlace de parámetros de componentes.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="f4b8f-181">Se prefiere `EventCallback<T>` (fuertemente tipado) a `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="f4b8f-182">`EventCallback<T>` proporciona mejores comentarios de errores a los usuarios del componente.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="f4b8f-183">Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="f4b8f-184">Use `EventCallback` cuando no se pase ningún valor a la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="f4b8f-185">Bloqueo de acciones predeterminadas</span><span class="sxs-lookup"><span data-stu-id="f4b8f-185">Prevent default actions</span></span>

<span data-ttu-id="f4b8f-186">Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="f4b8f-187">Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="f4b8f-188">En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="f4b8f-189">El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="f4b8f-190">Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="f4b8f-191">El valor del atributo también puede ser una expresión.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="f4b8f-192">En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-192">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="f4b8f-193">No se necesita un controlador de eventos para impedir la acción predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="f4b8f-194">El controlador de eventos y el bloqueo de escenarios de acción predeterminados se pueden usar de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="f4b8f-195">Detención de la propagación de eventos</span><span class="sxs-lookup"><span data-stu-id="f4b8f-195">Stop event propagation</span></span>

<span data-ttu-id="f4b8f-196">Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="f4b8f-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="f4b8f-197">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:</span><span class="sxs-lookup"><span data-stu-id="f4b8f-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
