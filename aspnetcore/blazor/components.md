---
title: Creación y uso de componentes de Razor de ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo crear y usar componentes de Razor, incluido cómo enlazar a datos, controlar eventos y administrar los ciclos de vida de los componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: bc1d07aef9cd60b89343a034168daa6754f4421b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306508"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Creación y uso de componentes de Razor de ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

Las aplicaciones Blazor se crean usando *componentes*. Un componente es un fragmento independiente de la interfaz de usuario, como una página, un cuadro de diálogo o un formulario. Los componentes incluyen el marcado HTML y la lógica de procesamiento necesarios para insertar datos o responder a eventos de la interfaz de usuario. Además, son flexibles y ligeros, y se pueden anidar, reutilizar y compartir entre proyectos.

## <a name="component-classes"></a>Clases de componentes

Los componentes se implementan en archivos de componentes de [Razor](xref:mvc/views/razor) ( *.razor*) usando una combinación de C# y marcado HTML. Un componente en Blazor se conoce formalmente como *componente de Razor*.

El nombre de un componente debe empezar por mayúsculas. Por ejemplo, *MyCoolComponent.razor* es válido, mientras que *myCoolComponent.razor* no.

La interfaz de usuario de un componente se define mediante HTML. La lógica de la representación dinámica (por ejemplo, bucles, instrucciones condicionales, expresiones) se agrega mediante una sintaxis de C# insertada denominada [Razor](xref:mvc/views/razor). Cuando una aplicación se compila, el marcado HTML y la lógica de representación de C# se convierten en una clase de componente. El nombre de la clase generada coincide con el nombre del archivo.

Los miembros de la clase de componente se definen en un bloque `@code`. En el bloque `@code`, el estado del componente (propiedades, campos) se especifica con métodos de control de eventos o con objeto de definir otra lógica de componente. Se permite emplear más de un bloque `@code`.

Los miembros de un componente se pueden usar como parte de la lógica de representación del componente mediante expresiones de C# que comienzan por `@`. Por ejemplo, un campo de C# se representa anteponiendo el prefijo `@` al nombre del campo. En el siguiente ejemplo se evalúa y representa lo siguiente:

* `_headingFontStyle` del valor de la propiedad CSS de `font-style`
* `_headingText` del contenido del elemento `<h1>`

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Una vez que el componente se ha representado inicialmente, este vuelve a generar su árbol de representación en respuesta a eventos. Blazor compara el nuevo árbol de representación con el anterior y aplica las modificaciones necesarias al explorador de Document Object Model (DOM).

Los componentes son clases de C# normales, y se pueden colocar en cualquier parte dentro de un proyecto. Los componentes que generan páginas web suelen residir en la carpeta *Pages*. Los componentes que no son de página colocan con frecuencia en la carpeta *Shared* o en una carpeta personalizada agregada al proyecto.

Por lo general, el espacio de nombres de un componente se deriva del espacio de nombres raíz de la aplicación y de la ubicación del componente (carpeta) dentro de la aplicación. Así, si el espacio de nombres raíz de la aplicación es `BlazorApp` y el componente `Counter` reside en la carpeta *Pages*:

* El espacio de nombres del componente `Counter` es `BlazorApp.Pages`.
* El nombre de tipo completo del componente es `BlazorApp.Pages.Counter`.

Para más información, vea la sección [Importación de componentes](#import-components).

Para usar una carpeta personalizada, agregue el espacio de nombres de la carpeta personalizada al componente primario o al archivo *_Imports.razor* de la aplicación. Por ejemplo, el siguiente espacio de nombres hace que los componentes incluidos en una carpeta *Components* estén disponibles cuando el espacio de nombres raíz de la aplicación es `BlazorApp`:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Recursos estáticos

Blazor sigue la convención de las aplicaciones de ASP.NET Core consistente en colocar los activos estáticos en la [carpeta de raíz web (wwwroot)](xref:fundamentals/index#web-root) del proyecto.

Use una ruta de acceso relativa base (`/`) para hacer referencia a la raíz web de un activo estático. En el siguiente ejemplo, *logo.png* se encuentra físicamente en la carpeta *{RAÍZ DEL PROYECTO}/wwwroot/images*:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Los componentes de Razor **no** admiten la notación de tilde de la ñ-barra diagonal (`~/`).

Para más información sobre cómo establecer la ruta de acceso base de una aplicación, vea <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Las aplicaciones auxiliares de etiquetas no se admiten en los componentes

Las [aplicaciones auxiliares de etiquetas](xref:mvc/views/tag-helpers/intro) no se pueden usar en los componentes de Razor (archivos *.razor*). Para proporcionar una funcionalidad similar a la de las aplicaciones auxiliares de etiquetas en Blazor, cree un componente con la misma funcionalidad que la aplicación auxiliar de etiquetas y use el componente en su lugar.

## <a name="use-components"></a>Uso de componentes

Para que los componentes puedan incluir otros componentes, hay que declararlos usando la sintaxis de elementos HTML. El marcado para utilizar un componente se parece a una etiqueta HTML en la que el nombre de la etiqueta es el tipo de componente.

El siguiente marcado en *Index.razor* representa una instancia de `HeadingComponent`:

```razor
<HeadingComponent />
```

*Components/HeadingComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Si un componente contiene un elemento HTML cuyo nombre empieza por mayúsculas y no coincide con un nombre de componente, se emite una advertencia que indica que el elemento tiene un nombre inesperado. Si se agrega una directiva `@using` relativa al espacio de nombres del componente, esto permite que el componente esté disponible, lo que resuelve la advertencia.

## <a name="routing"></a>Enrutamiento

El enrutamiento en Blazor se consigue proporcionando una plantilla de ruta a cada componente accesible en la aplicación.

Cuando se compila un archivo de Razor con una directiva `@page`, la clase generada recibe un elemento <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, donde se especifica la plantilla de ruta. En tiempo de ejecución, el enrutador busca las clases de componentes con un atributo `RouteAttribute`, y representa el componente que tenga una plantilla de ruta que coincida con la dirección URL solicitada.

```razor
@page "/ParentComponent"

...
```

Para obtener más información, vea <xref:blazor/routing>.

## <a name="parameters"></a>Parámetros

### <a name="route-parameters"></a>Parámetros de ruta

Los componentes pueden recibir parámetros de ruta de la plantilla de ruta proporcionada en la directiva `@page`. El enrutador usa parámetros de ruta para rellenar los parámetros de componente correspondientes con el mismo nombre.

*Pages/RouteParameter.razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Los parámetros opcionales no se admiten, por lo que en el ejemplo anterior se aplican dos directivas `@page`. La primera permite navegar al componente sin un parámetro, mientras que la segunda recibe el parámetro de ruta `{text}` y asigna el valor a la propiedad `Text`.

Los componentes de Razor ( *.razor*) **no** admiten la sintaxis de parámetro *catch-all* (`*`/`**`), que captura la ruta de acceso a lo largo de varios límites de carpeta.

### <a name="component-parameters"></a>Parámetros del componente

Los componentes pueden tener *parámetros de componente*, que se definen por medio de propiedades públicas en la clase del componente con el atributo `[Parameter]`. Use atributos para especificar argumentos para un componente en el marcado.

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

En el siguiente ejemplo de la aplicación de muestra, `ParentComponent` establece el valor de la propiedad `Title` de `ChildComponent`.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>Contenido secundario

Los componentes pueden definir el contenido de otro componente. El componente que asigna proporciona el contenido entre las etiquetas que especifican el componente receptor.

En el siguiente ejemplo, `ChildComponent` tiene una propiedad `ChildContent` que representa un elemento `RenderFragment`, que representa a su vez un segmento de interfaz de usuario que se va a representar. El valor de `ChildContent` se coloca en el marcado del componente donde el contenido debe representarse. El valor de `ChildContent` se recibe desde el componente primario, y se representa dentro del elemento `panel-body` del panel de arranque.

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> La propiedad que recibe el contenido de `RenderFragment` debe denominarse `ChildContent` por convención.

El elemento `ParentComponent` de la aplicación de muestra puede proporcionar contenido para representar el elemento `ChildComponent` colocando el contenido dentro de las etiquetas `<ChildComponent>`.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Expansión de atributos y parámetros arbitrarios

Los componentes pueden capturar y representar más atributos aparte de los parámetros declarados del componente. Estos atributos extra se pueden capturar en un diccionario y, después, *expandirse* en un elemento cuando el componente se represente por medio de la directiva de Razor [`@attributes`](xref:mvc/views/razor#attributes). Este escenario es útil cuando se define un componente que genera un elemento de marcado que admite diversas personalizaciones. Por ejemplo, definir atributos por separado para un elemento `<input>` que admite muchos parámetros puede ser un engorro.

En el siguiente ejemplo, el primer elemento `<input>` (`id="useIndividualParams"`) usa parámetros de componente individuales, mientras que el segundo elemento `<input>` (`id="useAttributesDict"`) usa la expansión de atributos:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

El tipo del parámetro debe implementar `IEnumerable<KeyValuePair<string, object>>` con claves de cadena. El uso de `IReadOnlyDictionary<string, object>` también es una opción en este escenario.

Los elementos `<input>` representados con ambos métodos son idénticos:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Para aceptar atributos arbitrarios, defina un parámetro de componente usando el atributo `[Parameter]` con la propiedad `CaptureUnmatchedValues` establecida en `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

La propiedad `CaptureUnmatchedValues` en `[Parameter]` permite que el parámetro coincida con todos los atributos que no coinciden con ningún otro parámetro. Un componente solamente puede definir un parámetro con `CaptureUnmatchedValues`. El tipo de propiedad que se usa con `CaptureUnmatchedValues` se debe poder asignar desde `Dictionary<string, object>` con claves de cadena. `IEnumerable<KeyValuePair<string, object>>` o `IReadOnlyDictionary<string, object>` también son opciones en este escenario.

La posición de `@attributes` relativa a la posición de los atributos de elemento es importante. Cuando `@attributes` se expande en el elemento, los atributos se procesan de derecha a izquierda (de último a primero). Veamos el siguiente ejemplo de un componente que usa un componente de `Child`:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

El atributo `extra` del componente de `Child` se establece a la derecha de `@attributes`. El elemento `<div>` representado del componente `Parent` contiene `extra="5"` cuando se pasa a través del atributo adicional, ya que los atributos se procesan de derecha a izquierda (de último a primero):

```html
<div extra="5" />
```

En el siguiente ejemplo, el orden de `extra` y `@attributes` se invierte en el elemento `<div>` del componente `Child`:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

El elemento `<div>` representado en el componente `Parent` contiene `extra="10"` cuando se pasa a través del atributo adicional:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Captura de referencias a componentes

Las referencias de componentes son una forma de hacer referencia a una instancia de componente para poder emitir comandos a dicha instancia, como `Show` o `Reset`. Para capturar una referencia de componente:

* Agregue un atributo [`@ref`](xref:mvc/views/razor#ref) al componente secundario.
* Defina un campo con el mismo tipo que el componente secundario.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

Cuando el componente se represente, el campo `_loginDialog` se rellena con la instancia del componente secundario `MyLoginDialog`. Tras ello, se pueden invocar métodos de .NET en la instancia del componente.

> [!IMPORTANT]
> La variable `_loginDialog` solo se rellena después de que el componente se represente, y su salida incluye el elemento `MyLoginDialog`. Hasta ese momento, no hay nada a lo que hacer referencia. Para manipular las referencias de componente una vez finalizada la representación del componente, use los [métodos OnAfterRenderAsync u OnAfterRender](xref:blazor/lifecycle#after-component-render).

Para hacer referencias a componentes de un bucle, consulte el artículo sobre la [captura de referencias a varios componentes secundarios similares (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Si bien la captura de referencias de componentes emplea una sintaxis similar a la de la [captura de referencias de elementos](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), no es una característica de interoperabilidad de JavaScript. Las referencias de componentes no se pasan a código de JavaScript; se usan única y exclusivamente en código de .NET.

> [!NOTE]
> **No** use referencias de componentes para mutar el estado de los componentes secundarios. En su lugar, use parámetros declarativos normales para pasar datos a componentes secundarios. El uso de parámetros declarativos normales da como resultado componentes secundarios que se representarán automáticamente justo cuando corresponda.

## <a name="invoke-component-methods-externally-to-update-state"></a>Invocación de métodos de componentes externamente para actualizar el estado

Blazor usa un elemento `SynchronizationContext` para aplicar un único subproceso lógico de ejecución. Los [métodos de ciclo de vida](xref:blazor/lifecycle) de un componente y todas las devoluciones de llamada de eventos que Blazor genere se ejecutan en dicho elemento `SynchronizationContext`. En caso de que un componente deba actualizarse en función de un evento externo, como un temporizador u otras notificaciones, use el método `InvokeAsync`, que enviará al elemento `SynchronizationContext` de Blazor.

Consideremos, por ejemplo, un *servicio de notificador* capaz de notificar el estado actualizado a cualquier componente de escucha:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Registre el elemento `NotifierService` como un singleton:

* En WebAssembly de Blazor, registre el servicio en `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* En el servidor Blazor, registre el servicio en `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Use el elemento `NotifierService` para actualizar un componente:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

En el ejemplo anterior, `NotifierService` invoca el método de `OnNotify` del componente fuera del elemento `SynchronizationContext` de Blazor. `InvokeAsync` se utiliza para cambiar al contexto correcto y poner una representación en cola.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Uso de \@key para controlar la conservación de elementos y componentes

Cuando se representa una lista de elementos o componentes, y esos elementos o componentes cambian posteriormente, el algoritmo de comparación de Blazor debe decidir cuáles de los elementos o componentes anteriores se pueden conservar y cómo asignar objetos de modelo a ellos. Normalmente, este proceso es automático y se puede pasar por alto, pero hay casos en los que puede que queramos controlarlo.

Considere el ejemplo siguiente:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

El contenido de la colección `People` puede cambiar porque se inserten, eliminen o reordenen entradas. Cuando el componente se representa, el componente `<DetailsEditor>` puede cambiar para recibir diferentes valores de parámetro `Details`. Esto puede hacer que se genere una nueva representación más compleja de lo esperado. En algunos casos, la nueva representación puede producir diferencias de comportamiento visibles, como la pérdida de foco de un elemento.

El proceso de asignación se puede controlar con el atributo de directiva `@key`. `@key` hace que el algoritmo de comparación de componentes garantice la conservación de elementos o componentes en función del valor de la clave:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Cuando la colección `People` cambia, el algoritmo de comparación mantiene la asociación entre las instancias de `<DetailsEditor>` y las instancias de `person`:

* Si un elemento `Person` se elimina de la lista `People`, solo se quitará de la interfaz de usuario la instancia de `<DetailsEditor>` correspondiente. Las demás instancias permanecerán inalteradas.
* Si se inserta un elemento `Person` en una posición dentro de la lista, se insertará una nueva instancia de `<DetailsEditor>` en la posición correspondiente. Las demás instancias permanecerán inalteradas.
* Si las entradas `Person` se reordenan, las instancias de `<DetailsEditor>` correspondientes se conservarán y reordenarán en la interfaz de usuario.

En algunos escenarios, el uso de `@key` reduce la complejidad de la nueva representación y evita posibles problemas con las partes con estado del DOM que cambia, como la posición del foco.

> [!IMPORTANT]
> Las claves son locales de cada componente o elemento contenedor. Las claves no se comparan globalmente en todo el documento.

### <a name="when-to-use-key"></a>Cuándo usar \@key

Normalmente, usar `@key` tiene sentido cada vez que una lista se represente (por ejemplo, en un bloque `@foreach`) y haya un valor adecuado para definir el elemento `@key`.

`@key` también se puede usar para impedir que Blazor conserve un subárbol de componente o elemento cuando un objeto cambie:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Si `@currentPerson` cambia, la directiva de atributo `@key` fuerza a Blazor a descartar el elemento `<div>` entero y sus descendientes, y vuelve a generar el subárbol dentro de la interfaz de usuario con nuevos elementos y componentes. Esto puede ser útil si debemos asegurarnos de que no se conserva ningún estado de interfaz de usuario cuando `@currentPerson` cambie.

### <a name="when-not-to-use-key"></a>Cuándo no usar \@key

Las comparaciones con `@key` repercuten en el rendimiento. El rendimiento no se ve especialmente afectado, pero pese a ello debemos especificar `@key` únicamente cuando controlar las reglas de conservación de componentes o elementos suponga un beneficio para la aplicación.

Aun cuando `@key` no se use, Blazor conserva las instancias de componentes y elemento secundarios lo máximo posible. La única ventaja de utilizar `@key` es el control sobre *cómo* se asignan instancias de modelo a las instancias de componente conservadas, en lugar del algoritmo de comparación, que selecciona la asignación.

### <a name="what-values-to-use-for-key"></a>Qué valores usar en \@key

Por lo general, lo lógico es proporcionar uno de los siguientes tipos de valor en `@key`:

* Instancias de objetos de modelo (una instancia de `Person`, como en el ejemplo anterior). Esto garantiza la conservación en función de la igualdad de las referencias de objetos.
* Identificadores únicos (por ejemplo, los valores de clave principal de tipo `int`, `string` o `Guid`).

Asegúrese de que los valores usados en `@key` no entran en conflicto. Si se detectan valores en conflicto en el mismo elemento primario, Blazor produce una excepción porque no puede asignar de forma determinista elementos o componentes antiguos a nuevos elementos o componentes. Use exclusivamente valores distintos, como instancias de objeto o valores de clave principal.

## <a name="partial-class-support"></a>Compatibilidad parcial de clases

Los componentes de Razor se generan como clases parciales. Los componentes de Razor se crean mediante cualquiera de los siguientes métodos:

* Se define código de C# en un bloque [`@code`](xref:mvc/views/razor#code) con marcado HTML y código de Razor en un mismo archivo. Las plantillas de Blazor definen sus propios componentes de Razor con este método.
* El código de C# se coloca en un archivo de código subyacente definido como una clase parcial.

En el siguiente ejemplo se muestra el componente `Counter` predeterminado con un bloque `@code` en una aplicación generada a partir de una plantilla de Blazor. El marcado HTML, el código de Razor y el código de C# se encuentran en el mismo archivo:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

El componente `Counter` también se puede crear con un archivo de código subyacente con una clase parcial:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

Agregue los espacios de nombres que sean necesarios al archivo de clase parcial. Los espacios de nombres típicos utilizados por los componentes de Razor son estos:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Especificación de una clase base

La directiva [`@inherits`](xref:mvc/views/razor#inherits) se puede usar para especificar la clase base de un componente. En el siguiente ejemplo se muestra cómo un componente puede heredar una clase base, `BlazorRocksBase`, para proporcionar las propiedades y los métodos del componente. La clase base debe derivar de `ComponentBase`.

*Pages/BlazorRocks.razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>Especificación de un atributo

En los componentes de Razor se pueden especificar atributos mediante la directiva [`@attribute`](xref:mvc/views/razor#attribute). En el siguiente ejemplo se aplica el atributo `[Authorize]` a la clase del componente:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importación de componentes

El espacio de nombres de un componente creado con Razor se basa en lo siguiente (por orden de prioridad):

* Designación de [`@namespace`](xref:mvc/views/razor#namespace) en el marcado del archivo de Razor ( *.razor*) (`@namespace BlazorSample.MyNamespace`).
* Elemento `RootNamespace` del proyecto en el archivo de proyecto (`<RootNamespace>BlazorSample</RootNamespace>`).
* Nombre del proyecto, tomado del nombre de archivo del archivo de proyecto ( *.csproj*) y la ruta de acceso de la raíz del proyecto al componente. Por ejemplo, el marco de trabajo resuelve *{RAÍZ DEL PROYECTO}/Pages/Index.razor* (*BlazorSample.csproj*) en el espacio de nombres `BlazorSample.Pages`. Los componentes de C# siguen las reglas de los enlaces de nombres. En el caso del componente `Index` de este ejemplo, los componentes en el ámbito serían todos los componentes:
  * En la misma carpeta, *Pages*.
  * Los componentes en la raíz del proyecto que no especifiquen explícitamente un espacio de nombres diferente.

Los componentes definidos en un espacio de nombres diferente se incluyen en el ámbito usando la directiva [`@using`](xref:mvc/views/razor#using) de Razor.

Si existe otro componente, `NavMenu.razor`, en la carpeta *BlazorSample/Shared/* , dicho componente se puede usar en `Index.razor` con la siguiente instrucción `@using`:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

También se puede hacer referencia a los componentes mediante sus nombres completos, lo cual no requiere el uso de la directiva [`@using`](xref:mvc/views/razor#using):

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> La calificación `global::` no se puede usar.
>
> No se pueden importar componentes con instrucciones `using` con alias (por ejemplo, `@using Foo = Bar`).
>
> No se pueden usar nombres parciales. Por ejemplo, no se puede agregar `@using BlazorSample` y hacer referencia a `NavMenu.razor` con `<Shared.NavMenu></Shared.NavMenu>`.

## <a name="conditional-html-element-attributes"></a>Atributos de elementos HTML condicionales

Los atributos de elementos HTML se representan condicionalmente en función del valor de .NET. Si el valor es `false` o `null`, el atributo no se representa. Si el valor es `true`, el atributo se representa minimizado.

En el siguiente ejemplo, `IsCompleted` determina si `checked` se representa en el marcado del elemento:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Si `IsCompleted` es `true`, la casilla se representa así:

```html
<input type="checkbox" checked />
```

Si `IsCompleted` es `false`, la casilla se representa así:

```html
<input type="checkbox" />
```

Para obtener más información, vea <xref:mvc/views/razor>.

> [!WARNING]
> Algunos atributos HTML, como [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), no funcionan correctamente cuando el tipo de .NET es `bool`. En esos casos, use un tipo `string` en lugar de `bool`.

## <a name="raw-html"></a>HTML sin formato

Normalmente, las cadenas se representan mediante nodos de texto DOM, lo que significa que cualquier marcado que esas cadenas puedan contener se omite y se trata como texto literal. Para representar HTML sin formato, encapsule el contenido HTML en un valor `MarkupString`. El valor se analiza como HTML o SVG y se inserta en el DOM.

> [!WARNING]
> La representación de HTML sin formato creado a partir de un origen que no es de confianza entraña un **riesgo de seguridad** y debe evitarse.

En el siguiente ejemplo se describe cómo usar el tipo `MarkupString` para agregar un bloque de contenido HTML estático a la salida representada de un componente:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Valores y parámetros en cascada

En algunos escenarios, no es conveniente que los datos fluyan desde un componente antecesor a un componente descendiente usando [parámetros de componente](#component-parameters), sobre todo si hay varios niveles de componentes. Los valores y parámetros en cascada ponen fin a este problema, ya que constituyen un método cómodo para que un componente antecesor proporcione un valor a todos sus componentes descendientes. Los valores y parámetros en cascada también sirven para que los componentes se coordinen.

### <a name="theme-example"></a>Ejemplo de Theme

En el siguiente ejemplo de la aplicación de muestra, la clase `ThemeInfo` especifica la información de tema que va a fluir hacia abajo en la jerarquía de componentes para que todos los botones de una parte determinada de la aplicación compartan el mismo estilo.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Un componente antecesor puede proporcionar un valor en cascada mediante el componente CascadingValue. El componente `CascadingValue` encapsula un subárbol de la jerarquía de componentes y proporciona un mismo valor para todos los componentes de ese subárbol.

Por ejemplo, en la aplicación de muestra se especifica información de tema (`ThemeInfo`) en uno de los diseños de la aplicación como un parámetro en cascada de todos los componentes que componen el cuerpo del diseño de la propiedad `@Body`. A `ButtonClass` se le asigna un valor `btn-success` en el componente de diseño. Cualquier componente descendiente puede usar esta propiedad a lo largo del objeto en cascada `ThemeInfo`.

Componente `CascadingValuesParametersLayout`:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Para usar valores en cascada, los componentes declaran parámetros en cascada mediante el atributo `[CascadingParameter]`. Los valores en cascada se enlazan a los parámetros en cascada según el tipo.

En la aplicación de muestra, el componente `CascadingValuesParametersTheme` enlaza el valor en cascada `ThemeInfo` a un parámetro en cascada. El parámetro se usa para establecer la clase CSS para uno de los botones que el componente muestra.

Componente `CascadingValuesParametersTheme`:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

Para poner en cascada varios valores del mismo tipo en un mismo subárbol, proporcione una cadena `Name` única en cada componente `CascadingValue` y su `CascadingParameter` correspondiente. En el siguiente ejemplo, dos componentes `CascadingValue` en cascada son instancias distintas de `MyCascadingType` por su nombre:

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

En un componente descendiente, los parámetros en cascada reciben sus valores de los valores en cascada correspondientes del componente antecesor por su nombre:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Ejemplo de TabSet

Los parámetros en cascada también permiten que los componentes colaboren a lo largo de la jerarquía de componentes. Veamos el siguiente ejemplo de *TabSet* en la aplicación de muestra.

La aplicación de muestra tiene una interfaz `ITab` que las pestañas implementan:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

El componente `CascadingValuesParametersTabSet` usa el componente `TabSet`, que contiene varios componentes `Tab`:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

Los componentes `Tab` secundarios no se pasan explícitamente como parámetros a `TabSet`, sino que forman parte del contenido secundario de `TabSet`. Pese a ello, `TabSet` sigue necesitando saber de cada componente `Tab` para poder representar los encabezados y la pestaña activa. Para permitir esta coordinación sin requerir código extra, el componente `TabSet` *puede proporcionarse a sí mismo como un valor en cascada* que, luego, van a seleccionar los componentes `Tab` descendientes.

Componente `TabSet`:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Los componentes `Tab` descendientes capturan el elemento `TabSet` contenedor como un parámetro en cascada, por lo que los componentes `Tab` se agregan a sí mismo a `TabSet` y coordinan en qué pestaña está activo.

Componente `Tab`:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Plantillas de Razor

Se pueden definir fragmentos de representación usando la sintaxis de plantilla de Razor. Las plantillas de Razor son un modo de definir un fragmento de interfaz de usuario y asumen el siguiente formato:

```razor
@<{HTML tag}>...</{HTML tag}>
```

En el siguiente ejemplo se muestra cómo especificar los valores `RenderFragment` y `RenderFragment<T>` y las plantillas de representación directamente en un componente. Los fragmentos de representación también se pueden pasar como argumentos a [componentes con plantilla](xref:blazor/templated-components).

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Salida representada del código anterior:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Imágenes con formato Scalable Vector Graphics (SVG)

Como Blazor representa HTML, se pueden usar imágenes compatibles con el explorador, incluidas imágenes con formato Scalable Vector Graphics (SVG) ( *.svg*), mediante la etiqueta `<img>`:

```html
<img alt="Example image" src="some-image.svg" />
```

Del mismo modo, se pueden usar imágenes SVG en las reglas de CSS de un archivo de hoja de estilos ( *.css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Pero no todos los escenarios admiten el uso de marcado SVG en línea. Si se coloca una etiqueta `<svg>` directamente en un archivo de componente ( *.razor*), se puede usar la representación de imágenes básica, pero muchos escenarios avanzados no estarán admitidos. Por ejemplo, actualmente las etiquetas `<use>` no se cumplen, y `@bind` no se puede usar con algunas etiquetas SVG. Esperamos abordar estas limitaciones en una versión futura.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/blazor/server>: incluye instrucciones sobre cómo crear aplicaciones de servidor Blazor que deben afrontar situaciones de agotamiento de recursos.
