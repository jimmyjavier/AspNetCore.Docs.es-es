---
title: Control de eventos de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 32f7595cffc2c31116c8d876c9f9526b84c52f14
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103466"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="3de0f-103">Control de eventos de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3de0f-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="3de0f-104">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3de0f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3de0f-105">Los componentes de Razor proporcionan características de control de eventos.</span><span class="sxs-lookup"><span data-stu-id="3de0f-105">Razor components provide event handling features.</span></span> <span data-ttu-id="3de0f-106">Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="3de0f-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="3de0f-107">El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="3de0f-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="3de0f-108">El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="3de0f-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="3de0f-109">Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="3de0f-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="3de0f-110">No es necesario llamar a [StateHasChanged](xref:blazor/components/lifecycle#state-changes) de forma manual.</span><span class="sxs-lookup"><span data-stu-id="3de0f-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="3de0f-111">Las excepciones se registran cuando se producen.</span><span class="sxs-lookup"><span data-stu-id="3de0f-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="3de0f-112">En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="3de0f-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="3de0f-113">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="3de0f-113">Event argument types</span></span>

<span data-ttu-id="3de0f-114">En algunos eventos, se permiten los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="3de0f-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="3de0f-115">Solo es necesario especificar un tipo de evento en la llamada de método si el tipo de evento se usa en el método.</span><span class="sxs-lookup"><span data-stu-id="3de0f-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="3de0f-116">En la tabla siguiente se muestran los valores <xref:System.EventArgs> admitidos.</span><span class="sxs-lookup"><span data-stu-id="3de0f-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="3de0f-117">evento</span><span class="sxs-lookup"><span data-stu-id="3de0f-117">Event</span></span>            | <span data-ttu-id="3de0f-118">Clase</span><span class="sxs-lookup"><span data-stu-id="3de0f-118">Class</span></span>                | <span data-ttu-id="3de0f-119">Eventos y notas de DOM</span><span class="sxs-lookup"><span data-stu-id="3de0f-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="3de0f-120">Portapapeles</span><span class="sxs-lookup"><span data-stu-id="3de0f-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="3de0f-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="3de0f-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="3de0f-122">Arrastrar</span><span class="sxs-lookup"><span data-stu-id="3de0f-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="3de0f-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="3de0f-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="3de0f-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> y <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contienen datos de elementos arrastrados.</span><span class="sxs-lookup"><span data-stu-id="3de0f-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="3de0f-125">Error</span><span class="sxs-lookup"><span data-stu-id="3de0f-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="3de0f-126">evento</span><span class="sxs-lookup"><span data-stu-id="3de0f-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="3de0f-127">*General*</span><span class="sxs-lookup"><span data-stu-id="3de0f-127">*General*</span></span><br><span data-ttu-id="3de0f-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="3de0f-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="3de0f-129">*Portapapeles*</span><span class="sxs-lookup"><span data-stu-id="3de0f-129">*Clipboard*</span></span><br><span data-ttu-id="3de0f-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="3de0f-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="3de0f-131">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="3de0f-131">*Input*</span></span><br><span data-ttu-id="3de0f-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="3de0f-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="3de0f-133">*Elementos multimedia*</span><span class="sxs-lookup"><span data-stu-id="3de0f-133">*Media*</span></span><br><span data-ttu-id="3de0f-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="3de0f-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="3de0f-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contiene atributos para configurar las asignaciones entre los nombres de evento y los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="3de0f-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="3de0f-136">Foco</span><span class="sxs-lookup"><span data-stu-id="3de0f-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="3de0f-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="3de0f-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="3de0f-138">No incluye compatibilidad con `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="3de0f-139">Entrada</span><span class="sxs-lookup"><span data-stu-id="3de0f-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="3de0f-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="3de0f-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="3de0f-141">Teclado</span><span class="sxs-lookup"><span data-stu-id="3de0f-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="3de0f-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="3de0f-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="3de0f-143">Mouse</span><span class="sxs-lookup"><span data-stu-id="3de0f-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="3de0f-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="3de0f-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="3de0f-145">Puntero del mouse</span><span class="sxs-lookup"><span data-stu-id="3de0f-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="3de0f-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="3de0f-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="3de0f-147">Rueda del mouse</span><span class="sxs-lookup"><span data-stu-id="3de0f-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="3de0f-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="3de0f-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="3de0f-149">Progreso</span><span class="sxs-lookup"><span data-stu-id="3de0f-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="3de0f-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="3de0f-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="3de0f-151">Entrada táctil</span><span class="sxs-lookup"><span data-stu-id="3de0f-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="3de0f-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="3de0f-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="3de0f-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa un único punto de contacto en un dispositivo sensible al tacto.</span><span class="sxs-lookup"><span data-stu-id="3de0f-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="3de0f-154">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="3de0f-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="3de0f-155">[Clases EventArgs en el origen de referencia de ASP.NET Core (dotnet/versión de aspnetcore/rama 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="3de0f-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="3de0f-156">[Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): incluye información sobre qué elementos HTML admite cada evento DOM.</span><span class="sxs-lookup"><span data-stu-id="3de0f-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="3de0f-157">Expresiones lambda</span><span class="sxs-lookup"><span data-stu-id="3de0f-157">Lambda expressions</span></span>

<span data-ttu-id="3de0f-158">También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="3de0f-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="3de0f-159">A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="3de0f-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="3de0f-160">En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="3de0f-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="3de0f-161">**No** use una variable de bucle directamente en una expresión lambda (como `i` en el ejemplo de bucle `for` anterior) o una variable de referencia en un bucle `foreach`;</span><span class="sxs-lookup"><span data-stu-id="3de0f-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="3de0f-162">de lo contrario, todas las expresiones lambda usarán la misma variable, con lo cual se usará el mismo valor en todas las expresiones lambda.</span><span class="sxs-lookup"><span data-stu-id="3de0f-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="3de0f-163">Capture siempre el valor de la variable en una variable local y úsela.</span><span class="sxs-lookup"><span data-stu-id="3de0f-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="3de0f-164">En el ejemplo anterior, la variable de bucle `i` se asigna a `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="3de0f-165">EventCallback</span><span class="sxs-lookup"><span data-stu-id="3de0f-165">EventCallback</span></span>

<span data-ttu-id="3de0f-166">Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="3de0f-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="3de0f-167">Un caso habitual es un evento `onclick` que se produce en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="3de0f-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="3de0f-168">Para exponer eventos entre componentes, use un elemento <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="3de0f-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="3de0f-169">Un componente primario puede asignar un método de devolución de llamada al elemento <xref:Microsoft.AspNetCore.Components.EventCallback> de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="3de0f-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="3de0f-170">El elemento `ChildComponent` de la aplicación de ejemplo (*Components/ChildComponent.razor*) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de <xref:Microsoft.AspNetCore.Components.EventCallback> del objeto `ParentComponent` del ejemplo.</span><span class="sxs-lookup"><span data-stu-id="3de0f-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="3de0f-171">El elemento <xref:Microsoft.AspNetCore.Components.EventCallback> tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="3de0f-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="3de0f-172">`ParentComponent` establece el objeto <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="3de0f-173">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="3de0f-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="3de0f-174">Cuando el botón se selecciona en `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="3de0f-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="3de0f-175">Se llama al método `ShowMessage` de `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="3de0f-176">`messageText` se actualiza y se muestra en `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="3de0f-177">No se requiere una llamada a [StateHasChanged](xref:blazor/components/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="3de0f-177">A call to [StateHasChanged](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="3de0f-178">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="3de0f-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="3de0f-179"><xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> permiten delegados asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="3de0f-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="3de0f-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> está fuertemente tipado y requiere un tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="3de0f-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="3de0f-181"><xref:Microsoft.AspNetCore.Components.EventCallback> está débilmente tipado y permite cualquier tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="3de0f-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="3de0f-182">Invoque <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> y espere a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="3de0f-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="3de0f-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> para el control de eventos y el enlace de parámetros de componentes.</span><span class="sxs-lookup"><span data-stu-id="3de0f-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="3de0f-184">Se prefiere <xref:Microsoft.AspNetCore.Components.EventCallback%601> (fuertemente tipado) a <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="3de0f-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="3de0f-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> proporciona mejores comentarios de errores a los usuarios del componente.</span><span class="sxs-lookup"><span data-stu-id="3de0f-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="3de0f-186">Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional.</span><span class="sxs-lookup"><span data-stu-id="3de0f-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="3de0f-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> cuando no se pase ningún valor a la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="3de0f-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="3de0f-188">Bloqueo de acciones predeterminadas</span><span class="sxs-lookup"><span data-stu-id="3de0f-188">Prevent default actions</span></span>

<span data-ttu-id="3de0f-189">Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="3de0f-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="3de0f-190">Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="3de0f-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="3de0f-191">En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="3de0f-192">El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="3de0f-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="3de0f-193">Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="3de0f-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="3de0f-194">El valor del atributo también puede ser una expresión.</span><span class="sxs-lookup"><span data-stu-id="3de0f-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="3de0f-195">En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:</span><span class="sxs-lookup"><span data-stu-id="3de0f-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="3de0f-196">No se necesita un controlador de eventos para impedir la acción predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3de0f-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="3de0f-197">El controlador de eventos y el bloqueo de escenarios de acción predeterminados se pueden usar de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="3de0f-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="3de0f-198">Detención de la propagación de eventos</span><span class="sxs-lookup"><span data-stu-id="3de0f-198">Stop event propagation</span></span>

<span data-ttu-id="3de0f-199">Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="3de0f-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="3de0f-200">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:</span><span class="sxs-lookup"><span data-stu-id="3de0f-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
