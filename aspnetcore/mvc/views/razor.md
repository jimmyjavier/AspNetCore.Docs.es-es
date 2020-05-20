---
title: referencia de la sintaxis de Razor para ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la Razor Sintaxis de marcado para insertar código basado en servidor en páginas Web.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424421"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="52ff9-103">Referencia de sintaxis de Razor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52ff9-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="52ff9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)y [dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="52ff9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="52ff9-105">Razor es una sintaxis de marcado para insertar código basado en servidor en páginas web.</span><span class="sxs-lookup"><span data-stu-id="52ff9-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="52ff9-106">La sintaxis de Razor combina marcado de Razor, C# y HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="52ff9-107">Los archivos que contienen sintaxis de Razor suelen tener la extensión de archivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="52ff9-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="52ff9-108">Razor también se encuentra en los archivos de los [componentes de Razor](xref:blazor/components) (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="52ff9-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="52ff9-109">Representación de HTML</span><span class="sxs-lookup"><span data-stu-id="52ff9-109">Rendering HTML</span></span>

<span data-ttu-id="52ff9-110">El lenguaje de Razor predeterminado es HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-110">The default Razor language is HTML.</span></span> <span data-ttu-id="52ff9-111">Representar el HTML del marcado de Razor no difiere mucho de representar el HTML de un archivo HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="52ff9-112">El marcado HTML de los archivos de Razor *.cshtml* se representa en el servidor sin cambios.</span><span class="sxs-lookup"><span data-stu-id="52ff9-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="52ff9-113">Sintaxis de Razor</span><span class="sxs-lookup"><span data-stu-id="52ff9-113">Razor syntax</span></span>

<span data-ttu-id="52ff9-114">Razor admite C# y usa el símbolo `@` para realizar la transición de HTML a C#.</span><span class="sxs-lookup"><span data-stu-id="52ff9-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="52ff9-115">Razor evalúa las expresiones de C# y las representa en la salida HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="52ff9-116">Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](#razor-reserved-keywords), realiza una transición a un marcado específico de Razor;</span><span class="sxs-lookup"><span data-stu-id="52ff9-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="52ff9-117">en caso contrario, realiza la transición a C# simple.</span><span class="sxs-lookup"><span data-stu-id="52ff9-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="52ff9-118">Para hacer escape en un símbolo `@` en el marcado de Razor, use un segundo símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="52ff9-119">El código aparecerá en HTML con un solo símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="52ff9-120">El contenido y los atributos HTML que tienen direcciones de correo electrónico no tratan el símbolo `@` como un carácter de transición.</span><span class="sxs-lookup"><span data-stu-id="52ff9-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="52ff9-121">El análisis de Razor no se detiene en las direcciones de correo electrónico del siguiente ejemplo:</span><span class="sxs-lookup"><span data-stu-id="52ff9-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="52ff9-122">Expresiones de Razor implícitas</span><span class="sxs-lookup"><span data-stu-id="52ff9-122">Implicit Razor expressions</span></span>

<span data-ttu-id="52ff9-123">Las expresiones de Razor implícitas comienzan por `@`, seguido de código C#:</span><span class="sxs-lookup"><span data-stu-id="52ff9-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="52ff9-124">Con la excepción de la palabra clave de C# `await`, las expresiones implícitas no deben contener espacios.</span><span class="sxs-lookup"><span data-stu-id="52ff9-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="52ff9-125">Si la instrucción de C# tiene un final claro, se pueden entremezclar espacios:</span><span class="sxs-lookup"><span data-stu-id="52ff9-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="52ff9-126">Las expresiones implícitas **no pueden** contener tipos genéricos de C#, ya que los caracteres dentro de los corchetes (`<>`) se interpretan como una etiqueta HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="52ff9-127">El siguiente código **no** es válido:</span><span class="sxs-lookup"><span data-stu-id="52ff9-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="52ff9-128">El código anterior genera un error del compilador similar a uno de los siguientes:</span><span class="sxs-lookup"><span data-stu-id="52ff9-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="52ff9-129">El elemento "int" no estaba cerrado.</span><span class="sxs-lookup"><span data-stu-id="52ff9-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="52ff9-130">Todos los elementos deben ser de autocierre o tener una etiqueta de fin coincidente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="52ff9-131">No se puede convertir el grupo de métodos "GenericMethod" en el tipo no delegado "object".</span><span class="sxs-lookup"><span data-stu-id="52ff9-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="52ff9-132">¿Intentó invocar el método?</span><span class="sxs-lookup"><span data-stu-id="52ff9-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="52ff9-133">Las llamadas a método genéricas deben estar incluidas en una [expresión de Razor explícita](#explicit-razor-expressions) o en un [bloque de código de Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="52ff9-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="52ff9-134">Expresiones de Razor explícitas</span><span class="sxs-lookup"><span data-stu-id="52ff9-134">Explicit Razor expressions</span></span>

<span data-ttu-id="52ff9-135">Las expresiones explícitas de Razor constan de un símbolo `@` y paréntesis de apertura y de cierre.</span><span class="sxs-lookup"><span data-stu-id="52ff9-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="52ff9-136">Para representar la hora de la semana pasada, se usaría el siguiente marcado de Razor:</span><span class="sxs-lookup"><span data-stu-id="52ff9-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="52ff9-137">El contenido que haya entre paréntesis `@()` se evalúa y se representa en la salida.</span><span class="sxs-lookup"><span data-stu-id="52ff9-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="52ff9-138">Por lo general, las expresiones implícitas (que explicamos en la sección anterior) no pueden contener espacios.</span><span class="sxs-lookup"><span data-stu-id="52ff9-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="52ff9-139">En el siguiente código, una semana no se resta de la hora actual:</span><span class="sxs-lookup"><span data-stu-id="52ff9-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="52ff9-140">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="52ff9-141">Se pueden usar expresiones explícitas para concatenar texto con un resultado de la expresión:</span><span class="sxs-lookup"><span data-stu-id="52ff9-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="52ff9-142">Sin la expresión explícita, `<p>Age@joe.Age</p>` se trataría como una dirección de correo electrónico y se mostraría como `<p>Age@joe.Age</p>`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="52ff9-143">Pero, si se escribe como una expresión explícita, se representa `<p>Age33</p>`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="52ff9-144">Se pueden usar expresiones explícitas para representar la salida de métodos genéricos en los archivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="52ff9-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="52ff9-145">En el siguiente marcado se muestra cómo corregir el error mostrado anteriormente provocado por el uso de corchetes en un código C# genérico.</span><span class="sxs-lookup"><span data-stu-id="52ff9-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="52ff9-146">El código se escribe como una expresión explícita:</span><span class="sxs-lookup"><span data-stu-id="52ff9-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="52ff9-147">Codificación de expresiones</span><span class="sxs-lookup"><span data-stu-id="52ff9-147">Expression encoding</span></span>

<span data-ttu-id="52ff9-148">Las expresiones de C# que se evalúan como una cadena están codificadas en HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="52ff9-149">Las expresiones de C# que se evalúan como `IHtmlContent` se representan directamente a través de `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="52ff9-150">Las expresiones de C# que no se evalúan como `IHtmlContent` se convierten en una cadena por medio de `ToString` y se codifican antes de que se representen.</span><span class="sxs-lookup"><span data-stu-id="52ff9-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="52ff9-151">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="52ff9-152">El HTML se muestra en el explorador como:</span><span class="sxs-lookup"><span data-stu-id="52ff9-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="52ff9-153">La salida de `HtmlHelper.Raw` no está codificada, pero se representa como marcado HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="52ff9-154">Usar `HtmlHelper.Raw` en una entrada de usuario no saneada constituye un riesgo de seguridad.</span><span class="sxs-lookup"><span data-stu-id="52ff9-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="52ff9-155">Dicha entrada podría contener código JavaScript malintencionado u otras vulnerabilidades de seguridad.</span><span class="sxs-lookup"><span data-stu-id="52ff9-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="52ff9-156">Sanear una entrada de usuario es complicado.</span><span class="sxs-lookup"><span data-stu-id="52ff9-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="52ff9-157">Evite usar `HtmlHelper.Raw` con entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="52ff9-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="52ff9-158">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="52ff9-159">Bloques de código de Razor</span><span class="sxs-lookup"><span data-stu-id="52ff9-159">Razor code blocks</span></span>

<span data-ttu-id="52ff9-160">Los bloques de código de Razor comienzan por `@` y se insertan entre `{}`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="52ff9-161">A diferencia de las expresiones, el código de C# dentro de los bloques de código no se representa.</span><span class="sxs-lookup"><span data-stu-id="52ff9-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="52ff9-162">Las expresiones y los bloques de código de una vista comparten el mismo ámbito y se definen en orden:</span><span class="sxs-lookup"><span data-stu-id="52ff9-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="52ff9-163">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52ff9-164">En los bloques de código, declare las [funciones locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) con marcado para utilizarlas como métodos en la creación de plantillas:</span><span class="sxs-lookup"><span data-stu-id="52ff9-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="52ff9-165">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="52ff9-166">Transiciones implícitas</span><span class="sxs-lookup"><span data-stu-id="52ff9-166">Implicit transitions</span></span>

<span data-ttu-id="52ff9-167">El lenguaje predeterminado de un bloque de código es C#, pero la página de Razor puede volver al HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="52ff9-168">Transición delimitada explícita</span><span class="sxs-lookup"><span data-stu-id="52ff9-168">Explicit delimited transition</span></span>

<span data-ttu-id="52ff9-169">Para definir una subsección de un bloque de código que deba representar HTML, inserte los caracteres que quiera representar entre etiquetas de Razor `<text>`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="52ff9-170">Emplee este método para representar HTML que no esté insertado entre etiquetas HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="52ff9-171">Sin una etiqueta HTML o de Razor, se produce un error de tiempo de ejecución de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="52ff9-172">La etiqueta `<text>` es útil para controlar el espacio en blanco al representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="52ff9-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="52ff9-173">Solo se representa el contenido entre etiquetas `<text>`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="52ff9-174">En la salida HTML no hay espacios en blanco antes o después de la etiqueta `<text>`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="52ff9-175">Transición de línea explícita</span><span class="sxs-lookup"><span data-stu-id="52ff9-175">Explicit line transition</span></span>

<span data-ttu-id="52ff9-176">Para representar el resto de una línea completa como HTML dentro de un bloque de código, use la sintaxis `@:`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="52ff9-177">Sin el carácter `@:` en el código, se produce un error de tiempo de ejecución de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="52ff9-178">Si se incluyen caracteres `@` de más en un archivo de Razor, se pueden producir errores de compilador en las instrucciones más adelante en el bloque.</span><span class="sxs-lookup"><span data-stu-id="52ff9-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="52ff9-179">Estos errores de compilador pueden ser difíciles de entender porque el error real se produce antes del error notificado.</span><span class="sxs-lookup"><span data-stu-id="52ff9-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="52ff9-180">Este error es habitual después de combinar varias expresiones implícitas/explícitas en un mismo bloque de código.</span><span class="sxs-lookup"><span data-stu-id="52ff9-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="52ff9-181">Estructuras de control</span><span class="sxs-lookup"><span data-stu-id="52ff9-181">Control structures</span></span>

<span data-ttu-id="52ff9-182">Las estructuras de control son una extensión de los bloques de código.</span><span class="sxs-lookup"><span data-stu-id="52ff9-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="52ff9-183">Todos los aspectos de los bloques de código (transición a marcado, C# en línea) son válidos también en las siguientes estructuras:</span><span class="sxs-lookup"><span data-stu-id="52ff9-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="52ff9-184">Condicionales \@if, else if, else y \@switch</span><span class="sxs-lookup"><span data-stu-id="52ff9-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="52ff9-185">`@if` controla cuándo se ejecuta el código:</span><span class="sxs-lookup"><span data-stu-id="52ff9-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="52ff9-186">`else` y `else if` no necesitan el símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="52ff9-187">En el siguiente marcado se muestra cómo usar una instrucción switch:</span><span class="sxs-lookup"><span data-stu-id="52ff9-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="52ff9-188">Bucles \@for, \@foreach, \@while y \@do while</span><span class="sxs-lookup"><span data-stu-id="52ff9-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="52ff9-189">El HTML con plantilla se puede representar con instrucciones de control en bucle.</span><span class="sxs-lookup"><span data-stu-id="52ff9-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="52ff9-190">Para representar una lista de personas:</span><span class="sxs-lookup"><span data-stu-id="52ff9-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="52ff9-191">Se permiten las siguientes instrucciones en bucle:</span><span class="sxs-lookup"><span data-stu-id="52ff9-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="52ff9-192">Compuesto \@using</span><span class="sxs-lookup"><span data-stu-id="52ff9-192">Compound \@using</span></span>

<span data-ttu-id="52ff9-193">En C#, las instrucciones `using` se usan para garantizar que un objeto se elimina.</span><span class="sxs-lookup"><span data-stu-id="52ff9-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="52ff9-194">En Razor, el mismo mecanismo se emplea para crear asistentes de HTML que incluyen contenido adicional.</span><span class="sxs-lookup"><span data-stu-id="52ff9-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="52ff9-195">En el siguiente código, los asistentes de HTML representan una etiqueta `<form>` con la instrucción `@using`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="52ff9-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="52ff9-196">\@try, catch, finally</span></span>

<span data-ttu-id="52ff9-197">El control de excepciones es similar a C#:</span><span class="sxs-lookup"><span data-stu-id="52ff9-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="52ff9-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="52ff9-198">\@lock</span></span>

<span data-ttu-id="52ff9-199">Razor tiene la capacidad de proteger las secciones más importantes con instrucciones de bloqueo:</span><span class="sxs-lookup"><span data-stu-id="52ff9-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="52ff9-200">Comentarios</span><span class="sxs-lookup"><span data-stu-id="52ff9-200">Comments</span></span>

<span data-ttu-id="52ff9-201">Razor admite comentarios tanto de C# como HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="52ff9-202">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="52ff9-203">El servidor quitará los comentarios de Razor antes de mostrar la página web.</span><span class="sxs-lookup"><span data-stu-id="52ff9-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="52ff9-204">Razor usa `@*  *@` para delimitar comentarios.</span><span class="sxs-lookup"><span data-stu-id="52ff9-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="52ff9-205">El siguiente código está comentado, de modo que el servidor no representa ningún marcado:</span><span class="sxs-lookup"><span data-stu-id="52ff9-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="52ff9-206">Directivas</span><span class="sxs-lookup"><span data-stu-id="52ff9-206">Directives</span></span>

<span data-ttu-id="52ff9-207">Las directivas de Razor se representan en las expresiones implícitas con palabras clave reservadas seguidas del símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="52ff9-208">Normalmente, una directiva cambia la forma en que una vista se analiza, o bien habilita una funcionalidad diferente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="52ff9-209">Conocer el modo en que Razor genera el código de una vista hace que sea más fácil comprender cómo funcionan las directivas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="52ff9-210">El código genera una clase similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="52ff9-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="52ff9-211">Más adelante en este artículo, en la sección [Inspección de la clase C# de Razor generada por una vista](#inspect-the-razor-c-class-generated-for-a-view), se explica cómo ver esta clase generada.</span><span class="sxs-lookup"><span data-stu-id="52ff9-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="52ff9-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="52ff9-212">\@attribute</span></span>

<span data-ttu-id="52ff9-213">La directiva `@attribute` agrega el atributo especificado a la clase de la página o vista generada.</span><span class="sxs-lookup"><span data-stu-id="52ff9-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="52ff9-214">En el ejemplo siguiente se agrega el atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="52ff9-215">\@code</span><span class="sxs-lookup"><span data-stu-id="52ff9-215">\@code</span></span>

<span data-ttu-id="52ff9-216">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-217">El bloque `@code` habilita un [componente de Razor](xref:blazor/components) para que agregue miembros de C# (campos, propiedades y métodos) a un componente:</span><span class="sxs-lookup"><span data-stu-id="52ff9-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="52ff9-218">En el caso de los componentes de Razor, `@code` es un alias de [`@functions`](#functions) y se recomienda en exceso `@functions` .</span><span class="sxs-lookup"><span data-stu-id="52ff9-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="52ff9-219">Se permite emplear más de un bloque `@code`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="52ff9-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="52ff9-220">\@functions</span></span>

<span data-ttu-id="52ff9-221">La directiva `@functions` permite agregar miembros de C# (campos, propiedades y métodos) a la clase generada:</span><span class="sxs-lookup"><span data-stu-id="52ff9-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52ff9-222">En los [componentes de Razor](xref:blazor/components), use `@code` en lugar de `@functions` para agregar miembros de C#.</span><span class="sxs-lookup"><span data-stu-id="52ff9-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="52ff9-223">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="52ff9-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="52ff9-224">El código genera el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="52ff9-225">El siguiente código es la clase C# de Razor generada:</span><span class="sxs-lookup"><span data-stu-id="52ff9-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52ff9-226">Los métodos `@functions` se pueden usar para la creación de plantillas si están marcados:</span><span class="sxs-lookup"><span data-stu-id="52ff9-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="52ff9-227">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="52ff9-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="52ff9-228">\@implements</span></span>

<span data-ttu-id="52ff9-229">La directiva `@implements` implementa una interfaz para la clase generada.</span><span class="sxs-lookup"><span data-stu-id="52ff9-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="52ff9-230">En el ejemplo siguiente se implementa <xref:System.IDisposable?displayProperty=fullName> para que se pueda llamar al método <xref:System.IDisposable.Dispose*>:</span><span class="sxs-lookup"><span data-stu-id="52ff9-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="52ff9-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="52ff9-231">\@inherits</span></span>

<span data-ttu-id="52ff9-232">La directiva `@inherits` proporciona control total sobre la clase que la vista hereda:</span><span class="sxs-lookup"><span data-stu-id="52ff9-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="52ff9-233">El siguiente código es un tipo personalizado de página de Razor:</span><span class="sxs-lookup"><span data-stu-id="52ff9-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="52ff9-234">`CustomText` se muestra en una vista:</span><span class="sxs-lookup"><span data-stu-id="52ff9-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="52ff9-235">El código representa el siguiente HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="52ff9-236">`@model` y `@inherits` se pueden usar en la misma vista.</span><span class="sxs-lookup"><span data-stu-id="52ff9-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="52ff9-237">`@inherits` puede estar en un archivo *_ViewImports.cshtml* que la vista importa:</span><span class="sxs-lookup"><span data-stu-id="52ff9-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="52ff9-238">El siguiente código es un ejemplo de una vista fuertemente tipada:</span><span class="sxs-lookup"><span data-stu-id="52ff9-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="52ff9-239">Si "rick@contoso.com" se pasa en el modelo, la vista genera el siguiente marcado HTML:</span><span class="sxs-lookup"><span data-stu-id="52ff9-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="52ff9-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="52ff9-240">\@inject</span></span>

<span data-ttu-id="52ff9-241">La directiva `@inject` permite a la página de Razor insertar un servicio del [contenedor de servicios](xref:fundamentals/dependency-injection) en una vista.</span><span class="sxs-lookup"><span data-stu-id="52ff9-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="52ff9-242">Para más información, vea [Dependency injection into views](xref:mvc/views/dependency-injection) (Inserción de dependencias en vistas).</span><span class="sxs-lookup"><span data-stu-id="52ff9-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="52ff9-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="52ff9-243">\@layout</span></span>

<span data-ttu-id="52ff9-244">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-245">La directiva `@layout` especifica un diseño para un componente de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="52ff9-246">Los componentes de diseño se usan para evitar incoherencias y contenido duplicado en el código.</span><span class="sxs-lookup"><span data-stu-id="52ff9-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="52ff9-247">Para obtener más información, vea <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="52ff9-248">\@model</span><span class="sxs-lookup"><span data-stu-id="52ff9-248">\@model</span></span>

<span data-ttu-id="52ff9-249">*Este escenario solo se aplica a las vistas de MVC y Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="52ff9-250">La directiva `@model` especifica el tipo del modelo que se pasa a una vista o página:</span><span class="sxs-lookup"><span data-stu-id="52ff9-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="52ff9-251">En una aplicación ASP.NET Core MVC o de Razor Pages creada con cuentas de usuario individuales, *Views/Account/Login.cshtml* contiene la siguiente declaración de modelo:</span><span class="sxs-lookup"><span data-stu-id="52ff9-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="52ff9-252">La clase generada se hereda de `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="52ff9-253">Razor expone una propiedad `Model` para tener acceso al modelo que se ha pasado a la vista:</span><span class="sxs-lookup"><span data-stu-id="52ff9-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="52ff9-254">La directiva `@model` especifica el tipo de la propiedad `Model`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="52ff9-255">La directiva especifica el elemento `T` en `RazorPage<T>` de la clase generada de la que se deriva la vista.</span><span class="sxs-lookup"><span data-stu-id="52ff9-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="52ff9-256">Si la directiva `@model` no se especifica, la propiedad `Model` es de tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="52ff9-257">Para obtener más información, vea [modelos fuertemente tipados y la @model palabra clave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="52ff9-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="52ff9-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="52ff9-258">\@namespace</span></span>

<span data-ttu-id="52ff9-259">Directiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="52ff9-260">Establece el espacio de nombres de la clase de la página de Razor, la vista de MVC o el componente Razor generados.</span><span class="sxs-lookup"><span data-stu-id="52ff9-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="52ff9-261">Establece los espacios de nombres de las clases de páginas, vistas o componentes derivados de la raíz del archivo de importaciones más cercano en el árbol de directorios, *_ViewImports.cshtml* (vistas o páginas) o *_Imports.razor* (componentes de Razor).</span><span class="sxs-lookup"><span data-stu-id="52ff9-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="52ff9-262">En el ejemplo de Razor Pages que se muestra en la tabla siguiente:</span><span class="sxs-lookup"><span data-stu-id="52ff9-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="52ff9-263">Cada página importa *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="52ff9-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="52ff9-264">*Pages/_ViewImports.cshtml* contiene `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="52ff9-265">Cada página tiene `Hello.World` como raíz de su espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="52ff9-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="52ff9-266">Página</span><span class="sxs-lookup"><span data-stu-id="52ff9-266">Page</span></span>                                        | <span data-ttu-id="52ff9-267">Espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="52ff9-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="52ff9-268">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="52ff9-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="52ff9-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="52ff9-271">Las relaciones anteriores se aplican a los archivos de importación usados con las vistas de MVC y los componentes de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="52ff9-272">Cuando varios archivos de importación tienen una directiva `@namespace`, se usa el archivo más cercano a la página, vista o componente en el árbol de directorios para establecer el espacio de nombres raíz.</span><span class="sxs-lookup"><span data-stu-id="52ff9-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="52ff9-273">Si la carpeta *EvenMorePages* del ejemplo anterior tiene un archivo de importaciones con `@namespace Another.Planet` (o el archivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiene `@namespace Another.Planet`), el resultado es el que se muestra en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="52ff9-274">Página</span><span class="sxs-lookup"><span data-stu-id="52ff9-274">Page</span></span>                                        | <span data-ttu-id="52ff9-275">Espacio de nombres</span><span class="sxs-lookup"><span data-stu-id="52ff9-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="52ff9-276">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="52ff9-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="52ff9-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="52ff9-279">\@page</span><span class="sxs-lookup"><span data-stu-id="52ff9-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52ff9-280">La directiva `@page` tiene efectos diferentes en función del tipo de archivo en el que aparece.</span><span class="sxs-lookup"><span data-stu-id="52ff9-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="52ff9-281">Directiva:</span><span class="sxs-lookup"><span data-stu-id="52ff9-281">The directive:</span></span>

* <span data-ttu-id="52ff9-282">En un archivo *.cshtml*, indica que el archivo es una página de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="52ff9-283">Para más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes) y <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="52ff9-284">Especifica que un componente de Razor debería controlar las solicitudes directamente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="52ff9-285">Para obtener más información, vea <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52ff9-286">La directiva `@page` en la primera línea de un archivo *.cshtml* indica que el archivo es una página de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="52ff9-287">Para obtener más información, vea <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="52ff9-288">\@section</span><span class="sxs-lookup"><span data-stu-id="52ff9-288">\@section</span></span>

<span data-ttu-id="52ff9-289">*Este escenario solo se aplica a las vistas de MVC y Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="52ff9-290">La directiva `@section` se usa junto con los [diseños de MVC y Razor Pages](xref:mvc/views/layout) para permitir que las vistas o las páginas representen el contenido en diferentes partes de la página HTML.</span><span class="sxs-lookup"><span data-stu-id="52ff9-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="52ff9-291">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="52ff9-292">\@using</span><span class="sxs-lookup"><span data-stu-id="52ff9-292">\@using</span></span>

<span data-ttu-id="52ff9-293">La directiva `@using` agrega la directiva `using` de C# a la vista generada:</span><span class="sxs-lookup"><span data-stu-id="52ff9-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52ff9-294">En [los componentes de Razor](xref:blazor/components), `@using` también controla qué componentes están en el ámbito.</span><span class="sxs-lookup"><span data-stu-id="52ff9-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="52ff9-295">Atributos de la directiva</span><span class="sxs-lookup"><span data-stu-id="52ff9-295">Directive attributes</span></span>

<span data-ttu-id="52ff9-296">Los atributos de directiva Razor se representan mediante expresiones implícitas con palabras clave reservadas después del `@` símbolo.</span><span class="sxs-lookup"><span data-stu-id="52ff9-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="52ff9-297">Un atributo de directiva suele cambiar la forma en que se analiza un elemento o habilita una funcionalidad diferente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="52ff9-298">\@attributes</span><span class="sxs-lookup"><span data-stu-id="52ff9-298">\@attributes</span></span>

<span data-ttu-id="52ff9-299">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-300">`@attributes` permite que un componente represente atributos no declarados.</span><span class="sxs-lookup"><span data-stu-id="52ff9-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="52ff9-301">Para obtener más información, vea <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="52ff9-302">\@bind</span><span class="sxs-lookup"><span data-stu-id="52ff9-302">\@bind</span></span>

<span data-ttu-id="52ff9-303">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-304">El enlace de datos en los componentes se logra mediante el atributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="52ff9-305">Para obtener más información, vea <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="52ff9-306">\@on{EVENT}</span><span class="sxs-lookup"><span data-stu-id="52ff9-306">\@on{EVENT}</span></span>

<span data-ttu-id="52ff9-307">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-308">Razor proporciona características de control de eventos para componentes.</span><span class="sxs-lookup"><span data-stu-id="52ff9-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="52ff9-309">Para obtener más información, vea <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="52ff9-310">\@on{EVENT}:preventDefault</span><span class="sxs-lookup"><span data-stu-id="52ff9-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="52ff9-311">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-312">Impide la acción predeterminada para el evento.</span><span class="sxs-lookup"><span data-stu-id="52ff9-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="52ff9-313">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="52ff9-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="52ff9-314">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-315">Detiene la propagación de eventos para el evento.</span><span class="sxs-lookup"><span data-stu-id="52ff9-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="52ff9-316">\@key</span><span class="sxs-lookup"><span data-stu-id="52ff9-316">\@key</span></span>

<span data-ttu-id="52ff9-317">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-318">El atributo de directiva `@key` hace que el algoritmo de comparación de componentes garantice la preservación de elementos o componentes en función del valor de la clave.</span><span class="sxs-lookup"><span data-stu-id="52ff9-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="52ff9-319">Para obtener más información, vea <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="52ff9-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="52ff9-320">\@ref</span></span>

<span data-ttu-id="52ff9-321">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-322">Las referencias de componentes (`@ref`) proporcionan una forma de hacer referencia a la instancia de un componente para poder emitir comandos a dicha instancia.</span><span class="sxs-lookup"><span data-stu-id="52ff9-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="52ff9-323">Para obtener más información, vea <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="52ff9-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="52ff9-324">\@typeparam</span></span>

<span data-ttu-id="52ff9-325">*Este escenario solo se aplica a los componentes de Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="52ff9-326">La directiva `@typeparam` declara un parámetro de tipo genérico para la clase de componente generada.</span><span class="sxs-lookup"><span data-stu-id="52ff9-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="52ff9-327">Para obtener más información, vea <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="52ff9-328">Delegados con plantillas de Razor</span><span class="sxs-lookup"><span data-stu-id="52ff9-328">Templated Razor delegates</span></span>

<span data-ttu-id="52ff9-329">Las plantillas de Razor permiten definir un fragmento de la interfaz de usuario con el formato siguiente:</span><span class="sxs-lookup"><span data-stu-id="52ff9-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="52ff9-330">En el ejemplo siguiente se muestra cómo especificar un delegado de Razor con plantilla como elemento <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="52ff9-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="52ff9-331">El [tipo dinámico](/dotnet/csharp/programming-guide/types/using-type-dynamic) se especifica para el parámetro del método encapsulado por el delegado.</span><span class="sxs-lookup"><span data-stu-id="52ff9-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="52ff9-332">Se especifica un [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) como el valor devuelto del delegado.</span><span class="sxs-lookup"><span data-stu-id="52ff9-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="52ff9-333">La plantilla se usa con un elemento <xref:System.Collections.Generic.List%601> de `Pet` que tiene una propiedad `Name`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="52ff9-334">La plantilla se representa con el elemento `pets` proporcionado por una instrucción `foreach`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="52ff9-335">Salida representada:</span><span class="sxs-lookup"><span data-stu-id="52ff9-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="52ff9-336">También se puede proporcionar una plantilla de Razor insertada como un argumento para un método.</span><span class="sxs-lookup"><span data-stu-id="52ff9-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="52ff9-337">En el ejemplo siguiente, el método `Repeat` recibe una plantilla de Razor.</span><span class="sxs-lookup"><span data-stu-id="52ff9-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="52ff9-338">El método usa la plantilla para generar contenido HTML con repeticiones de elementos proporcionados a partir de una lista:</span><span class="sxs-lookup"><span data-stu-id="52ff9-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="52ff9-339">Con la lista de mascotas del ejemplo anterior, se llama al método `Repeat` con:</span><span class="sxs-lookup"><span data-stu-id="52ff9-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="52ff9-340"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="52ff9-341">Número de veces que se repite cada mascota.</span><span class="sxs-lookup"><span data-stu-id="52ff9-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="52ff9-342">Plantilla insertada que se va a usar para los elementos de una lista sin ordenar.</span><span class="sxs-lookup"><span data-stu-id="52ff9-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="52ff9-343">Salida representada:</span><span class="sxs-lookup"><span data-stu-id="52ff9-343">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="52ff9-344">Asistentes de etiquetas</span><span class="sxs-lookup"><span data-stu-id="52ff9-344">Tag Helpers</span></span>

<span data-ttu-id="52ff9-345">*Este escenario solo se aplica a las vistas de MVC y Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="52ff9-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="52ff9-346">Hay tres directivas que pertenecen a los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="52ff9-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="52ff9-347">Directiva</span><span class="sxs-lookup"><span data-stu-id="52ff9-347">Directive</span></span> | <span data-ttu-id="52ff9-348">Función</span><span class="sxs-lookup"><span data-stu-id="52ff9-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="52ff9-349">Pone los asistentes de etiquetas a disposición de una vista.</span><span class="sxs-lookup"><span data-stu-id="52ff9-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="52ff9-350">Quita los asistentes de etiquetas agregadas anteriormente desde una vista.</span><span class="sxs-lookup"><span data-stu-id="52ff9-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="52ff9-351">Especifica una cadena de prefijo de etiqueta para permitir la compatibilidad con el asistente de etiquetas y hacer explícito su uso.</span><span class="sxs-lookup"><span data-stu-id="52ff9-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="52ff9-352">Palabras clave reservadas</span><span class="sxs-lookup"><span data-stu-id="52ff9-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="52ff9-353">palabra</span><span class="sxs-lookup"><span data-stu-id="52ff9-353"> keywords</span></span>

* <span data-ttu-id="52ff9-354">page (requiere ASP.NET Core 2.1 o una versión posterior)</span><span class="sxs-lookup"><span data-stu-id="52ff9-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="52ff9-355">namespace</span><span class="sxs-lookup"><span data-stu-id="52ff9-355">namespace</span></span>
* <span data-ttu-id="52ff9-356">functions</span><span class="sxs-lookup"><span data-stu-id="52ff9-356">functions</span></span>
* <span data-ttu-id="52ff9-357">hereda</span><span class="sxs-lookup"><span data-stu-id="52ff9-357">inherits</span></span>
* <span data-ttu-id="52ff9-358">model</span><span class="sxs-lookup"><span data-stu-id="52ff9-358">model</span></span>
* <span data-ttu-id="52ff9-359">section</span><span class="sxs-lookup"><span data-stu-id="52ff9-359">section</span></span>
* <span data-ttu-id="52ff9-360">helper (no admitida en ASP.NET Core actualmente)</span><span class="sxs-lookup"><span data-stu-id="52ff9-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="52ff9-361">las palabras clave se incluyen en secuencias `@(Razor Keyword)` de escape (por ejemplo, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="52ff9-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="52ff9-362">RazorPalabras clave de C#</span><span class="sxs-lookup"><span data-stu-id="52ff9-362">C# Razor keywords</span></span>

* <span data-ttu-id="52ff9-363">mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="52ff9-363">case</span></span>
* <span data-ttu-id="52ff9-364">do</span><span class="sxs-lookup"><span data-stu-id="52ff9-364">do</span></span>
* <span data-ttu-id="52ff9-365">default</span><span class="sxs-lookup"><span data-stu-id="52ff9-365">default</span></span>
* <span data-ttu-id="52ff9-366">para</span><span class="sxs-lookup"><span data-stu-id="52ff9-366">for</span></span>
* <span data-ttu-id="52ff9-367">foreach</span><span class="sxs-lookup"><span data-stu-id="52ff9-367">foreach</span></span>
* <span data-ttu-id="52ff9-368">if</span><span class="sxs-lookup"><span data-stu-id="52ff9-368">if</span></span>
* <span data-ttu-id="52ff9-369">else</span><span class="sxs-lookup"><span data-stu-id="52ff9-369">else</span></span>
* <span data-ttu-id="52ff9-370">bloquear</span><span class="sxs-lookup"><span data-stu-id="52ff9-370">lock</span></span>
* <span data-ttu-id="52ff9-371">switch</span><span class="sxs-lookup"><span data-stu-id="52ff9-371">switch</span></span>
* <span data-ttu-id="52ff9-372">probar</span><span class="sxs-lookup"><span data-stu-id="52ff9-372">try</span></span>
* <span data-ttu-id="52ff9-373">catch</span><span class="sxs-lookup"><span data-stu-id="52ff9-373">catch</span></span>
* <span data-ttu-id="52ff9-374">finally</span><span class="sxs-lookup"><span data-stu-id="52ff9-374">finally</span></span>
* <span data-ttu-id="52ff9-375">using</span><span class="sxs-lookup"><span data-stu-id="52ff9-375">using</span></span>
* <span data-ttu-id="52ff9-376">while</span><span class="sxs-lookup"><span data-stu-id="52ff9-376">while</span></span>

<span data-ttu-id="52ff9-377">Las Razor palabras clave de C# deben tener un doble escape con `@(@C# Razor Keyword)` (por ejemplo, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="52ff9-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="52ff9-378">El primero `@` convierte el analizador en caracteres de escape Razor .</span><span class="sxs-lookup"><span data-stu-id="52ff9-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="52ff9-379">y el segundo `@`, en el analizador de C#.</span><span class="sxs-lookup"><span data-stu-id="52ff9-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="52ff9-380">Palabras clave reservadas no utilizadas porRazor</span><span class="sxs-lookup"><span data-stu-id="52ff9-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="52ff9-381">clase</span><span class="sxs-lookup"><span data-stu-id="52ff9-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="52ff9-382">Inspeccionar la Razor clase de C# generada para una vista</span><span class="sxs-lookup"><span data-stu-id="52ff9-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="52ff9-383">Con SDK de .NET Core 2,1 o posterior, el [ Razor SDK](xref:razor-pages/sdk) controla la compilación de Razor archivos.</span><span class="sxs-lookup"><span data-stu-id="52ff9-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="52ff9-384">Al compilar un proyecto, el Razor SDK genera un *build_configuration de <y de>/<Razor target_framework_moniker>/* directorio en la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="52ff9-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="52ff9-385">La estructura de directorios dentro del *Razor* directorio refleja la estructura de directorios del proyecto.</span><span class="sxs-lookup"><span data-stu-id="52ff9-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="52ff9-386">Considere la siguiente estructura de directorios en un proyecto de páginas de ASP.NET Core 2,1 que tiene Razor como destino .net Core 2,1:</span><span class="sxs-lookup"><span data-stu-id="52ff9-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="52ff9-387">**Lugares**</span><span class="sxs-lookup"><span data-stu-id="52ff9-387">**Areas/**</span></span>
  * <span data-ttu-id="52ff9-388">**Administrar**</span><span class="sxs-lookup"><span data-stu-id="52ff9-388">**Admin/**</span></span>
    * <span data-ttu-id="52ff9-389">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="52ff9-389">**Pages/**</span></span>
      * <span data-ttu-id="52ff9-390">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="52ff9-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="52ff9-392">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="52ff9-392">**Pages/**</span></span>
  * <span data-ttu-id="52ff9-393">**Recurso**</span><span class="sxs-lookup"><span data-stu-id="52ff9-393">**Shared/**</span></span>
    * <span data-ttu-id="52ff9-394">*_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="52ff9-395">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="52ff9-396">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="52ff9-397">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="52ff9-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="52ff9-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="52ff9-399">Al compilar el proyecto en la configuración *Depurar* se crea el directorio *obj* siguiente:</span><span class="sxs-lookup"><span data-stu-id="52ff9-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="52ff9-400">**obj**</span><span class="sxs-lookup"><span data-stu-id="52ff9-400">**obj/**</span></span>
  * <span data-ttu-id="52ff9-401">**Depura**</span><span class="sxs-lookup"><span data-stu-id="52ff9-401">**Debug/**</span></span>
    * <span data-ttu-id="52ff9-402">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="52ff9-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="52ff9-403">**Lugares**</span><span class="sxs-lookup"><span data-stu-id="52ff9-403">**Areas/**</span></span>
          * <span data-ttu-id="52ff9-404">**Administrar**</span><span class="sxs-lookup"><span data-stu-id="52ff9-404">**Admin/**</span></span>
            * <span data-ttu-id="52ff9-405">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="52ff9-405">**Pages/**</span></span>
              * <span data-ttu-id="52ff9-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="52ff9-407">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="52ff9-407">**Pages/**</span></span>
          * <span data-ttu-id="52ff9-408">**Recurso**</span><span class="sxs-lookup"><span data-stu-id="52ff9-408">**Shared/**</span></span>
            * <span data-ttu-id="52ff9-409">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="52ff9-410">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="52ff9-411">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="52ff9-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="52ff9-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="52ff9-413">Para ver la clase generada para *pages/index. cshtml*, Abra *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.CS*.</span><span class="sxs-lookup"><span data-stu-id="52ff9-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="52ff9-414">Agregue la siguiente clase al proyecto de ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="52ff9-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="52ff9-415">En `Startup.ConfigureServices`, invalide el elemento `RazorTemplateEngine` agregado por MVC con la clase `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="52ff9-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="52ff9-416">Establezca un punto de interrupción en la instrucción `return csharpDocument;` de `CustomTemplateEngine`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="52ff9-417">Cuando la ejecución del programa se detenga en el punto de interrupción, vea el valor de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="52ff9-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Vista del visualizador de texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="52ff9-419">Búsquedas de vistas y distinción entre mayúsculas y minúsculas</span><span class="sxs-lookup"><span data-stu-id="52ff9-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="52ff9-420">El Razor motor de vista realiza búsquedas con distinción de mayúsculas y minúsculas para las vistas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="52ff9-421">Pero la búsqueda real viene determinada por el sistema de archivos subyacente:</span><span class="sxs-lookup"><span data-stu-id="52ff9-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="52ff9-422">Origen basado en archivos:</span><span class="sxs-lookup"><span data-stu-id="52ff9-422">File based source:</span></span>
  * <span data-ttu-id="52ff9-423">En los sistemas operativos con sistemas de archivos que no distinguen entre mayúsculas y minúsculas (por ejemplo, Windows), las búsquedas de proveedor de archivos físicos no distinguirán mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="52ff9-424">Por ejemplo, `return View("Test")` arrojará como resultados */Views/Home/Test.cshtml*, */Views/home/test.cshtml* y cualquier otra variante de mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="52ff9-425">En los sistemas de archivos en los que sí se distingue entre mayúsculas y minúsculas (por ejemplo, Linux, OSX y al usar `EmbeddedFileProvider`), las búsquedas distinguirán mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="52ff9-426">Por ejemplo, `return View("Test")` mostrará el resultado */Views/Home/Test.cshtml* única y exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="52ff9-427">Vistas precompiladas: En ASP.NET Core 2.0 y versiones posteriores, las búsquedas de vistas precompiladas no distinguen mayúsculas de minúsculas en todos los sistemas operativos.</span><span class="sxs-lookup"><span data-stu-id="52ff9-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="52ff9-428">Este comportamiento es idéntico al comportamiento del proveedor de archivos físicos en Windows.</span><span class="sxs-lookup"><span data-stu-id="52ff9-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="52ff9-429">Si dos vistas precompiladas difieren solo por sus mayúsculas o minúsculas, el resultado de la búsqueda será no determinante.</span><span class="sxs-lookup"><span data-stu-id="52ff9-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="52ff9-430">Por tanto, se anima a todos los desarrolladores a intentar que las mayúsculas y minúsculas de los nombres de archivo y de directorio sean las mismas que las mayúsculas y minúsculas de:</span><span class="sxs-lookup"><span data-stu-id="52ff9-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="52ff9-431">Nombres de acciones, controladores y áreas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="52ff9-432">Páginas.</span><span class="sxs-lookup"><span data-stu-id="52ff9-432"> Pages.</span></span>

<span data-ttu-id="52ff9-433">La coincidencia de mayúsculas y minúsculas garantiza que las implementaciones van a encontrar sus vistas, independientemente de cuál sea el sistema de archivos subyacente.</span><span class="sxs-lookup"><span data-stu-id="52ff9-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52ff9-434">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="52ff9-434">Additional resources</span></span>

<span data-ttu-id="52ff9-435">[Introducción a la programación web de ASP.net mediante el Razor La sintaxis](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) proporciona muchos ejemplos de programación con Razor Sintaxis.</span><span class="sxs-lookup"><span data-stu-id="52ff9-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
