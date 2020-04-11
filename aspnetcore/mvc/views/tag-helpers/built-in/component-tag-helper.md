---
title: Ayudante de etiqueta de componente en ASP.NET Core
author: guardrex
ms.author: riande
description: Aprenda a utilizar ASP.NET Core Component Tag Helper para representar componentes de Razor en páginas y vistas.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123426"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Ayudante de etiqueta de componente en ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

Para representar un componente desde una página o vista, utilice la [aplicación auxiliar](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)de etiquetas de componente .

## <a name="prerequisites"></a>Prerrequisitos

Siga las instrucciones de la sección *Preparar la aplicación para usar componentes en páginas y vistas* del <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> artículo.

## <a name="component-tag-helper"></a>Component Tag Helper

La siguiente aplicación auxiliar `Counter` de etiquetas de componente representa el componente en una página o vista:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

En el ejemplo anterior `Counter` se supone que el componente se encuentra en la carpeta *Pages* de la aplicación.

La aplicación auxiliar de etiquetas de componentes también puede pasar parámetros a los componentes. Considere el `ColorfulCheckbox` siguiente componente que establece el color y el tamaño de la etiqueta de casilla de verificación:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

Los `Size` `int`parámetros `Color` `string`del [componente](xref:blazor/components#component-parameters) ( ) y ( ) se pueden establecer mediante la aplicación auxiliar de etiquetas de componente:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

En el ejemplo anterior `ColorfulCheckbox` se supone que el componente se encuentra en la carpeta *Shared* de la aplicación.

El siguiente código HTML se representa en la página o vista:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Pasar una cadena entre comillas requiere una expresión `param-Color` Razor [explícita,](xref:mvc/views/razor#explicit-razor-expressions)como se muestra en el ejemplo anterior. El comportamiento de análisis `string` de Razor para un `param-*` valor de tipo `object` no se aplica a un atributo porque el atributo es un tipo.

El tipo de parámetro debe ser serializable JSON, lo que normalmente significa que el tipo debe tener un constructor predeterminado y propiedades configurables. Por ejemplo, puede especificar `Size` un `Color` valor para y en `Size` el `Color` ejemplo anterior`int` `string`porque los tipos de y son tipos primitivos ( y ), que son compatibles con el serializador JSON.

En el ejemplo siguiente, se pasa un objeto de clase al componente:

*MyClass.cs*:

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**La clase debe tener un constructor público sin parámetros.**

*Shared/MyComponent.razor*:

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Páginas/MyPage.cshtml*:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

En el ejemplo anterior `MyComponent` se supone que el componente se encuentra en la carpeta *Shared* de la aplicación. `MyClass`está en el espacio`{APP ASSEMBLY}`de nombres de la aplicación ( ).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura si el componente:

* Se procesa previamente en la página.
* Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

| Modo de renderización | Descripción |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Representa el componente en HTML estático e Blazor incluye un marcador para una aplicación de servidor. Cuando se inicia el agente de usuario, Blazor este marcador se usa para arrancar una aplicación. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Representa un marcador Blazor para una aplicación de servidor. La salida del componente no está incluida. Cuando se inicia el agente de usuario, Blazor este marcador se usa para arrancar una aplicación. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente en HTML estático. |

Aunque las páginas y las vistas pueden usar componentes, la inversa no es verdadera. Los componentes no pueden usar características específicas de la vista y de la página, como vistas parciales y secciones. Para utilizar la lógica de una vista parcial en un componente, factorice la lógica de vista parcial en un componente.

No se admite la representación de componentes de servidor desde una página HTML estática.

## <a name="additional-resources"></a>Recursos adicionales

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
