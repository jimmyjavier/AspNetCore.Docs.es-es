---
title: referencia de la sintaxis de Razor para ASP.NET Core
author: rick-anderson
description: Obtenga información Razor sobre la sintaxis de marcado para insertar código basado en servidor en páginas Web.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 3e77b25e2660688d0040d47840e47dab8f260197
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003200"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Referencia de sintaxis de Razor para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)y [dan Vicarel](https://github.com/Rabadash8820)

Razor es una sintaxis de marcado para insertar código basado en servidor en páginas web. La sintaxis de Razor combina marcado de Razor, C# y HTML. Los archivos que contienen sintaxis de Razor suelen tener la extensión de archivo *.cshtml*. Razor también se encuentra en los archivos de los [componentes de Razor](xref:blazor/components) (*.razor*).

## <a name="rendering-html"></a>Representación de HTML

El lenguaje de Razor predeterminado es HTML. Representar el HTML del marcado de Razor no difiere mucho de representar el HTML de un archivo HTML. El marcado HTML de los archivos de Razor *.cshtml* se representa en el servidor sin cambios.

## <a name="razor-syntax"></a>Sintaxis de Razor

Razor admite C# y usa el símbolo `@` para realizar la transición de HTML a C#. Razor evalúa las expresiones de C# y las representa en la salida HTML.

Cuando el símbolo `@` va seguido de una [palabra clave reservada de Razor](#razor-reserved-keywords), realiza una transición a un marcado específico de Razor; en caso contrario, realiza la transición a C# simple.

Para hacer escape en un símbolo `@` en el marcado de Razor, use un segundo símbolo `@`:

```cshtml
<p>@@Username</p>
```

El código aparecerá en HTML con un solo símbolo `@`:

```html
<p>@Username</p>
```

El contenido y los atributos HTML que tienen direcciones de correo electrónico no tratan el símbolo `@` como un carácter de transición. El análisis de Razor no se detiene en las direcciones de correo electrónico del siguiente ejemplo:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Expresiones de Razor implícitas

Las expresiones de Razor implícitas comienzan por `@`, seguido de código C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Con la excepción de la palabra clave de C# `await`, las expresiones implícitas no deben contener espacios. Si la instrucción de C# tiene un final claro, se pueden entremezclar espacios:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Las expresiones implícitas **no pueden** contener tipos genéricos de C#, ya que los caracteres dentro de los corchetes (`<>`) se interpretan como una etiqueta HTML. El siguiente código **no** es válido:

```cshtml
<p>@GenericMethod<int>()</p>
```

El código anterior genera un error del compilador similar a uno de los siguientes:

* El elemento "int" no estaba cerrado. Todos los elementos deben ser de autocierre o tener una etiqueta de fin coincidente.
* No se puede convertir el grupo de métodos "GenericMethod" en el tipo no delegado "object". ¿Intentó invocar el método?

Las llamadas a método genéricas deben estar incluidas en una [expresión de Razor explícita](#explicit-razor-expressions) o en un [bloque de código de Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Expresiones de Razor explícitas

Las expresiones explícitas de Razor constan de un símbolo `@` y paréntesis de apertura y de cierre. Para representar la hora de la semana pasada, se usaría el siguiente marcado de Razor:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

El contenido que haya entre paréntesis `@()` se evalúa y se representa en la salida.

Por lo general, las expresiones implícitas (que explicamos en la sección anterior) no pueden contener espacios. En el siguiente código, una semana no se resta de la hora actual:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

El código representa el siguiente HTML:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Se pueden usar expresiones explícitas para concatenar texto con un resultado de la expresión:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sin la expresión explícita, `<p>Age@joe.Age</p>` se trataría como una dirección de correo electrónico y se mostraría como `<p>Age@joe.Age</p>`. Pero, si se escribe como una expresión explícita, se representa `<p>Age33</p>`.

Se pueden usar expresiones explícitas para representar la salida de métodos genéricos en los archivos *.cshtml*. En el siguiente marcado se muestra cómo corregir el error mostrado anteriormente provocado por el uso de corchetes en un código C# genérico. El código se escribe como una expresión explícita:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Codificación de expresiones

Las expresiones de C# que se evalúan como una cadena están codificadas en HTML. Las expresiones de C# que se evalúan como `IHtmlContent` se representan directamente a través de `IHtmlContent.WriteTo`. Las expresiones de C# que no se evalúan como `IHtmlContent` se convierten en una cadena por medio de `ToString` y se codifican antes de que se representen.

```cshtml
@("<span>Hello World</span>")
```

El código representa el siguiente HTML:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

El HTML se muestra en el explorador como:

```html
<span>Hello World</span>
```

La salida de `HtmlHelper.Raw` no está codificada, pero se representa como marcado HTML.

> [!WARNING]
> Usar `HtmlHelper.Raw` en una entrada de usuario no saneada constituye un riesgo de seguridad. Dicha entrada podría contener código JavaScript malintencionado u otras vulnerabilidades de seguridad. Sanear una entrada de usuario es complicado. Evite usar `HtmlHelper.Raw` con entradas de usuario.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

El código representa el siguiente HTML:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Bloques de código de Razor

Los bloques de código de Razor comienzan por `@` y se insertan entre `{}`. A diferencia de las expresiones, el código de C# dentro de los bloques de código no se representa. Las expresiones y los bloques de código de una vista comparten el mismo ámbito y se definen en orden:

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

El código representa el siguiente HTML:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

En los bloques de código, declare las [funciones locales](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) con marcado para utilizarlas como métodos en la creación de plantillas:

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

El código representa el siguiente HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Transiciones implícitas

El lenguaje predeterminado de un bloque de código es C#, pero la página de Razor puede volver al HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Transición delimitada explícita

Para definir una subsección de un bloque de código que deba representar HTML, inserte los caracteres que quiera representar entre etiquetas de Razor `<text>`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Emplee este método para representar HTML que no esté insertado entre etiquetas HTML. Sin una etiqueta HTML o de Razor, se produce un error de tiempo de ejecución de Razor.

La etiqueta `<text>` es útil para controlar el espacio en blanco al representar el contenido:

* Solo se representa el contenido entre etiquetas `<text>`.
* En la salida HTML no hay espacios en blanco antes o después de la etiqueta `<text>`.

### <a name="explicit-line-transition"></a>Transición de línea explícita

Para representar el resto de una línea completa como HTML dentro de un bloque de código, use la sintaxis `@:`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sin el carácter `@:` en el código, se produce un error de tiempo de ejecución de Razor.

Si se incluyen caracteres `@` de más en un archivo de Razor, se pueden producir errores de compilador en las instrucciones más adelante en el bloque. Estos errores de compilador pueden ser difíciles de entender porque el error real se produce antes del error notificado. Este error es habitual después de combinar varias expresiones implícitas/explícitas en un mismo bloque de código.

## <a name="control-structures"></a>Estructuras de control

Las estructuras de control son una extensión de los bloques de código. Todos los aspectos de los bloques de código (transición a marcado, C# en línea) son válidos también en las siguientes estructuras:

### <a name="conditionals-if-else-if-else-and-switch"></a>Condicionales \@if, else if, else y \@switch

`@if` controla cuándo se ejecuta el código:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` y `else if` no necesitan el símbolo `@`:

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

En el siguiente marcado se muestra cómo usar una instrucción switch:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Bucles \@for, \@foreach, \@while y \@do while

El HTML con plantilla se puede representar con instrucciones de control en bucle. Para representar una lista de personas:

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

Se permiten las siguientes instrucciones en bucle:

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

### <a name="compound-using"></a>Compuesto \@using

En C#, las instrucciones `using` se usan para garantizar que un objeto se elimina. En Razor, el mismo mecanismo se emplea para crear asistentes de HTML que incluyen contenido adicional. En el siguiente código, los asistentes de HTML representan una etiqueta `<form>` con la instrucción `@using`:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@try, catch, finally

El control de excepciones es similar a C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@lock

Razor tiene la capacidad de proteger las secciones más importantes con instrucciones de bloqueo:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Comentarios

Razor admite comentarios tanto de C# como HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

El código representa el siguiente HTML:

```html
<!-- HTML comment -->
```

El servidor quitará los comentarios de Razor antes de mostrar la página web. Razor usa `@*  *@` para delimitar comentarios. El siguiente código está comentado, de modo que el servidor no representa ningún marcado:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Directivas

Las directivas de Razor se representan en las expresiones implícitas con palabras clave reservadas seguidas del símbolo `@`. Normalmente, una directiva cambia la forma en que una vista se analiza, o bien habilita una funcionalidad diferente.

Conocer el modo en que Razor genera el código de una vista hace que sea más fácil comprender cómo funcionan las directivas.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

El código genera una clase similar a la siguiente:

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

Más adelante en este artículo, en la sección [Inspección de la clase C# de Razor generada por una vista](#inspect-the-razor-c-class-generated-for-a-view), se explica cómo ver esta clase generada.

### <a name="attribute"></a>\@attribute

La directiva `@attribute` agrega el atributo especificado a la clase de la página o vista generada. En el ejemplo siguiente se agrega el atributo `[Authorize]`:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@code

*Este escenario solo se aplica a los componentes de Razor (.razor).*

El bloque `@code` habilita un [componente de Razor](xref:blazor/components) para que agregue miembros de C# (campos, propiedades y métodos) a un componente:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

En el caso de `@code` los componentes de Razor [`@functions`](#functions) , es un `@functions`alias de y se recomienda en exceso. Se permite emplear más de un bloque `@code`.

::: moniker-end

### <a name="functions"></a>\@functions

La directiva `@functions` permite agregar miembros de C# (campos, propiedades y métodos) a la clase generada:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

En los [componentes de Razor](xref:blazor/components), use `@code` en lugar de `@functions` para agregar miembros de C#.

::: moniker-end

Por ejemplo:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

El código genera el siguiente marcado HTML:

```html
<div>From method: Hello</div>
```

El siguiente código es la clase C# de Razor generada:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

Los métodos `@functions` se pueden usar para la creación de plantillas si están marcados:

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

El código representa el siguiente HTML:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@implements

La directiva `@implements` implementa una interfaz para la clase generada.

En el ejemplo siguiente se implementa <xref:System.IDisposable?displayProperty=fullName> para que se pueda llamar al método <xref:System.IDisposable.Dispose*>:

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

### <a name="inherits"></a>\@inherits

La directiva `@inherits` proporciona control total sobre la clase que la vista hereda:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

El siguiente código es un tipo personalizado de página de Razor:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` se muestra en una vista:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

El código representa el siguiente HTML:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` y `@inherits` se pueden usar en la misma vista. `@inherits` puede estar en un archivo *_ViewImports.cshtml* que la vista importa:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

El siguiente código es un ejemplo de una vista fuertemente tipada:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Si "rick@contoso.com" se pasa en el modelo, la vista genera el siguiente marcado HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@inject

La directiva `@inject` permite a la página de Razor insertar un servicio del [contenedor de servicios](xref:fundamentals/dependency-injection) en una vista. Para más información, vea [Dependency injection into views](xref:mvc/views/dependency-injection) (Inserción de dependencias en vistas).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@layout

*Este escenario solo se aplica a los componentes de Razor (.razor).*

La directiva `@layout` especifica un diseño para un componente de Razor. Los componentes de diseño se usan para evitar incoherencias y contenido duplicado en el código. Para obtener más información, vea <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@model

*Este escenario solo se aplica a las vistas de MVC y Razor Pages (.cshtml).*

La directiva `@model` especifica el tipo del modelo que se pasa a una vista o página:

```cshtml
@model TypeNameOfModel
```

En una aplicación ASP.NET Core MVC o de Razor Pages creada con cuentas de usuario individuales, *Views/Account/Login.cshtml* contiene la siguiente declaración de modelo:

```cshtml
@model LoginViewModel
```

La clase generada se hereda de `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor expone una propiedad `Model` para tener acceso al modelo que se ha pasado a la vista:

```cshtml
<div>The Login Email: @Model.Email</div>
```

La directiva `@model` especifica el tipo de la propiedad `Model`. La directiva especifica el elemento `T` en `RazorPage<T>` de la clase generada de la que se deriva la vista. Si la directiva `@model` no se especifica, la propiedad `Model` es de tipo `dynamic`. Para obtener más información, vea [modelos fuertemente tipados y @model la palabra clave](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@namespace

Directiva `@namespace`:

* Establece el espacio de nombres de la clase de la página de Razor, la vista de MVC o el componente Razor generados.
* Establece los espacios de nombres de las clases de páginas, vistas o componentes derivados de la raíz del archivo de importaciones más cercano en el árbol de directorios, *_ViewImports.cshtml* (vistas o páginas) o *_Imports.razor* (componentes de Razor).

```cshtml
@namespace Your.Namespace.Here
```

En el ejemplo de Razor Pages que se muestra en la tabla siguiente:

* Cada página importa *Pages/_ViewImports.cshtml*.
* *Pages/_ViewImports.cshtml* contiene `@namespace Hello.World`.
* Cada página tiene `Hello.World` como raíz de su espacio de nombres.

| Página                                        | Espacio de nombres                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Las relaciones anteriores se aplican a los archivos de importación usados con las vistas de MVC y los componentes de Razor.

Cuando varios archivos de importación tienen una directiva `@namespace`, se usa el archivo más cercano a la página, vista o componente en el árbol de directorios para establecer el espacio de nombres raíz.

Si la carpeta *EvenMorePages* del ejemplo anterior tiene un archivo de importaciones con `@namespace Another.Planet` (o el archivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiene `@namespace Another.Planet`), el resultado es el que se muestra en la tabla siguiente.

| Página                                        | Espacio de nombres               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@page

::: moniker range=">= aspnetcore-3.0"

La directiva `@page` tiene efectos diferentes en función del tipo de archivo en el que aparece. Directiva:

* En un archivo *.cshtml*, indica que el archivo es una página de Razor. Para más información, consulte [Rutas personalizadas](xref:razor-pages/index#custom-routes) y <xref:razor-pages/index>.
* Especifica que un componente de Razor debería controlar las solicitudes directamente. Para obtener más información, vea <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

La directiva `@page` en la primera línea de un archivo *.cshtml* indica que el archivo es una página de Razor. Para obtener más información, vea <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@section

*Este escenario solo se aplica a las vistas de MVC y Razor Pages (.cshtml).*

La directiva `@section` se usa junto con los [diseños de MVC y Razor Pages](xref:mvc/views/layout) para permitir que las vistas o las páginas representen el contenido en diferentes partes de la página HTML. Para obtener más información, vea <xref:mvc/views/layout>.

### <a name="using"></a>\@using

La directiva `@using` agrega la directiva `using` de C# a la vista generada:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

En [los componentes](xref:blazor/components)de `@using` Razor, también controla qué componentes están en el ámbito.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Atributos de la directiva

### <a name="attributes"></a>\@attributes

*Este escenario solo se aplica a los componentes de Razor (.razor).*

`@attributes` permite que un componente represente atributos no declarados. Para obtener más información, vea <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@bind

*Este escenario solo se aplica a los componentes de Razor (.razor).*

El enlace de datos en los componentes se logra mediante el atributo `@bind`. Para obtener más información, vea <xref:blazor/data-binding>.

### <a name="onevent"></a>\@on{EVENT}

*Este escenario solo se aplica a los componentes de Razor (.razor).*

Razor proporciona características de control de eventos para componentes. Para obtener más información, vea <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@on{EVENT}:preventDefault

*Este escenario solo se aplica a los componentes de Razor (.razor).*

Impide la acción predeterminada para el evento.

### <a name="oneventstoppropagation"></a>\@on{EVENT}:stopPropagation

*Este escenario solo se aplica a los componentes de Razor (.razor).*

Detiene la propagación de eventos para el evento.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@key

*Este escenario solo se aplica a los componentes de Razor (.razor).*

El atributo de directiva `@key` hace que el algoritmo de comparación de componentes garantice la preservación de elementos o componentes en función del valor de la clave. Para obtener más información, vea <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Este escenario solo se aplica a los componentes de Razor (.razor).*

Las referencias de componentes (`@ref`) proporcionan una forma de hacer referencia a la instancia de un componente para poder emitir comandos a dicha instancia. Para obtener más información, vea <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Este escenario solo se aplica a los componentes de Razor (.razor).*

La directiva `@typeparam` declara un parámetro de tipo genérico para la clase de componente generada. Para obtener más información, vea <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Delegados con plantillas de Razor

Las plantillas de Razor permiten definir un fragmento de la interfaz de usuario con el formato siguiente:

```cshtml
@<tag>...</tag>
```

En el ejemplo siguiente se muestra cómo especificar un delegado de Razor con plantilla como elemento <xref:System.Func%602>. El [tipo dinámico](/dotnet/csharp/programming-guide/types/using-type-dynamic) se especifica para el parámetro del método encapsulado por el delegado. Se especifica un [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) como el valor devuelto del delegado. La plantilla se usa con un elemento <xref:System.Collections.Generic.List%601> de `Pet` que tiene una propiedad `Name`.

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

La plantilla se representa con el elemento `pets` proporcionado por una instrucción `foreach`:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Salida representada:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

También se puede proporcionar una plantilla de Razor insertada como un argumento para un método. En el ejemplo siguiente, el método `Repeat` recibe una plantilla de Razor. El método usa la plantilla para generar contenido HTML con repeticiones de elementos proporcionados a partir de una lista:

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

Con la lista de mascotas del ejemplo anterior, se llama al método `Repeat` con:

* <xref:System.Collections.Generic.List%601> de `Pet`.
* Número de veces que se repite cada mascota.
* Plantilla insertada que se va a usar para los elementos de una lista sin ordenar.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Salida representada:

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

## <a name="tag-helpers"></a>Asistentes de etiquetas

*Este escenario solo se aplica a las vistas de MVC y Razor Pages (.cshtml).*

Hay tres directivas que pertenecen a los [asistentes de etiquetas](xref:mvc/views/tag-helpers/intro).

| Directiva | Función |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Pone los asistentes de etiquetas a disposición de una vista. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Quita los asistentes de etiquetas agregadas anteriormente desde una vista. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Especifica una cadena de prefijo de etiqueta para permitir la compatibilidad con el asistente de etiquetas y hacer explícito su uso. |

## <a name="razor-reserved-keywords"></a>RazorPalabras clave reservadas

### <a name="razor-keywords"></a>Razorpalabra

* page (requiere ASP.NET Core 2.1 o una versión posterior)
* namespace
* functions
* hereda
* model
* section
* helper (no admitida en ASP.NET Core actualmente)

Razorlas palabras clave se `@(Razor Keyword)` incluyen en secuencias de `@(functions)`escape (por ejemplo,).

### <a name="c-razor-keywords"></a>Palabras Razor clave de C#

* mayúsculas y minúsculas
* do
* default
* for
* foreach
* if
* else
* bloquear
* switch
* probar
* catch
* finally
* using
* while

Las Razor palabras clave de C# deben tener un `@(@C# Razor Keyword)` doble escape con ( `@(@case)`por ejemplo,). El primero `@` convierte el analizador Razor en caracteres de escape. y el segundo `@`, en el analizador de C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Palabras clave reservadas no utilizadas porRazor

* clase

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Inspeccionar Razor la clase de C# generada para una vista

::: moniker range=">= aspnetcore-2.1"

Con SDK de .net Core 2,1 o posterior, el [ Razor SDK](xref:razor-pages/sdk) controla la compilación Razor de archivos. Al compilar un proyecto Razor , el SDK genera un *build_configuration de <y de>/<Razor target_framework_moniker>/* directorio en la raíz del proyecto. La estructura de directorios dentro *Razor* del directorio refleja la estructura de directorios del proyecto.

Considere la siguiente estructura de directorios en un proyecto Razor de páginas de ASP.net Core 2,1 que tiene como destino .net Core 2,1:

* **Lugares**
  * **Administrar**
    * **Páginas**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Páginas**
  * **Recurso**
    * *_Layout. cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart. cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

Al compilar el proyecto en la configuración *Depurar* se crea el directorio *obj* siguiente:

* **obj**
  * **Depura**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Lugares**
          * **Administrar**
            * **Páginas**
              * *Index.g.cshtml.cs*
        * **Páginas**
          * **Recurso**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Para ver la clase generada para *pages/index. cshtml*, Abra *obj/Debug/netcoreappRazor2.1//Pages/index.g.cshtml.CS*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Agregue la siguiente clase al proyecto de ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

En `Startup.ConfigureServices`, invalide el elemento `RazorTemplateEngine` agregado por MVC con la clase `CustomTemplateEngine`:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Establezca un punto de interrupción en la instrucción `return csharpDocument;` de `CustomTemplateEngine`. Cuando la ejecución del programa se detenga en el punto de interrupción, vea el valor de `generatedCode`.

![Vista del visualizador de texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Búsquedas de vistas y distinción entre mayúsculas y minúsculas

El Razor motor de vista realiza búsquedas con distinción de mayúsculas y minúsculas para las vistas. Pero la búsqueda real viene determinada por el sistema de archivos subyacente:

* Origen basado en archivos:
  * En los sistemas operativos con sistemas de archivos que no distinguen entre mayúsculas y minúsculas (por ejemplo, Windows), las búsquedas de proveedor de archivos físicos no distinguirán mayúsculas de minúsculas. Por ejemplo, `return View("Test")` arrojará como resultados */Views/Home/Test.cshtml*, */Views/home/test.cshtml* y cualquier otra variante de mayúsculas y minúsculas.
  * En los sistemas de archivos en los que sí se distingue entre mayúsculas y minúsculas (por ejemplo, Linux, OSX y al usar `EmbeddedFileProvider`), las búsquedas distinguirán mayúsculas de minúsculas. Por ejemplo, `return View("Test")` mostrará el resultado */Views/Home/Test.cshtml* única y exclusivamente.
* Vistas precompiladas: En ASP.NET Core 2.0 y versiones posteriores, las búsquedas de vistas precompiladas no distinguen mayúsculas de minúsculas en todos los sistemas operativos. Este comportamiento es idéntico al comportamiento del proveedor de archivos físicos en Windows. Si dos vistas precompiladas difieren solo por sus mayúsculas o minúsculas, el resultado de la búsqueda será no determinante.

Por tanto, se anima a todos los desarrolladores a intentar que las mayúsculas y minúsculas de los nombres de archivo y de directorio sean las mismas que las mayúsculas y minúsculas de:

* Nombres de acciones, controladores y áreas.
* RazorPáginas.

La coincidencia de mayúsculas y minúsculas garantiza que las implementaciones van a encontrar sus vistas, independientemente de cuál sea el sistema de archivos subyacente.

## <a name="additional-resources"></a>Recursos adicionales

[La introducción a la programación web de Razor ASP.net mediante la sintaxis](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) proporciona muchos ejemplos Razor de programación con sintaxis.
