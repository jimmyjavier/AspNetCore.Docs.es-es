---
title: Ayudante de etiqueta de componente en ASP.NET Core
author: guardrex
ms.author: riande
description: Aprenda a utilizar ASP.NET Core Component Tag Helper para representar componentes de Razor en páginas y vistas.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440966"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="e17dd-103">Ayudante de etiqueta de componente en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e17dd-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e17dd-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e17dd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e17dd-105">Para representar un componente desde una página o vista, utilice la [aplicación auxiliar](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)de etiquetas de componente .</span><span class="sxs-lookup"><span data-stu-id="e17dd-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e17dd-106">Prerrequisitos</span><span class="sxs-lookup"><span data-stu-id="e17dd-106">Prerequisites</span></span>

<span data-ttu-id="e17dd-107">Siga las instrucciones de la sección *Preparar la aplicación para usar componentes en páginas y vistas* del <xref:blazor/integrate-components#prepare-the-app> artículo.</span><span class="sxs-lookup"><span data-stu-id="e17dd-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="e17dd-108">Component Tag Helper</span><span class="sxs-lookup"><span data-stu-id="e17dd-108">Component Tag Helper</span></span>

<span data-ttu-id="e17dd-109">La siguiente aplicación auxiliar `Counter` de etiquetas de componente representa el componente en una página o vista:</span><span class="sxs-lookup"><span data-stu-id="e17dd-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="e17dd-110">En el ejemplo anterior `Counter` se supone que el componente se encuentra en la carpeta *Pages* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e17dd-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="e17dd-111">La aplicación auxiliar de etiquetas de componentes también puede pasar parámetros a los componentes.</span><span class="sxs-lookup"><span data-stu-id="e17dd-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="e17dd-112">Considere el `ColorfulCheckbox` siguiente componente que establece el color y el tamaño de la etiqueta de casilla de verificación:</span><span class="sxs-lookup"><span data-stu-id="e17dd-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="e17dd-113">Los `Size` `int`parámetros `Color` `string`del [componente](xref:blazor/components#component-parameters) ( ) y ( ) se pueden establecer mediante la aplicación auxiliar de etiquetas de componente:</span><span class="sxs-lookup"><span data-stu-id="e17dd-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="e17dd-114">En el ejemplo anterior `ColorfulCheckbox` se supone que el componente se encuentra en la carpeta *Shared* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e17dd-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="e17dd-115">El siguiente código HTML se representa en la página o vista:</span><span class="sxs-lookup"><span data-stu-id="e17dd-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="e17dd-116">Pasar una cadena entre comillas requiere una expresión `param-Color` Razor [explícita,](xref:mvc/views/razor#explicit-razor-expressions)como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="e17dd-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="e17dd-117">El comportamiento de análisis `string` de Razor para un `param-*` valor de tipo `object` no se aplica a un atributo porque el atributo es un tipo.</span><span class="sxs-lookup"><span data-stu-id="e17dd-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="e17dd-118">El tipo de parámetro debe ser serializable JSON, lo que normalmente significa que el tipo debe tener un constructor predeterminado y propiedades configurables.</span><span class="sxs-lookup"><span data-stu-id="e17dd-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="e17dd-119">Por ejemplo, puede especificar `Size` un `Color` valor para y en `Size` el `Color` ejemplo anterior`int` `string`porque los tipos de y son tipos primitivos ( y ), que son compatibles con el serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="e17dd-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="e17dd-120">En el ejemplo siguiente, se pasa un objeto de clase al componente:</span><span class="sxs-lookup"><span data-stu-id="e17dd-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="e17dd-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="e17dd-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="e17dd-122">**La clase debe tener un constructor público sin parámetros.**</span><span class="sxs-lookup"><span data-stu-id="e17dd-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="e17dd-123">*Shared/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="e17dd-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="e17dd-124">*Páginas/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="e17dd-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="e17dd-125">En el ejemplo anterior `MyComponent` se supone que el componente se encuentra en la carpeta *Shared* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e17dd-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="e17dd-126">`MyClass`está en el espacio`{APP ASSEMBLY}`de nombres de la aplicación ( ).</span><span class="sxs-lookup"><span data-stu-id="e17dd-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="e17dd-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="e17dd-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e17dd-128">Se procesa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="e17dd-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="e17dd-129">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="e17dd-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="e17dd-130">Modo de renderización</span><span class="sxs-lookup"><span data-stu-id="e17dd-130">Render Mode</span></span> | <span data-ttu-id="e17dd-131">Descripción</span><span class="sxs-lookup"><span data-stu-id="e17dd-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e17dd-132">Representa el componente en HTML estático e Blazor incluye un marcador para una aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="e17dd-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e17dd-133">Cuando se inicia el agente de usuario, Blazor este marcador se usa para arrancar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e17dd-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e17dd-134">Representa un marcador Blazor para una aplicación de servidor.</span><span class="sxs-lookup"><span data-stu-id="e17dd-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e17dd-135">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="e17dd-135">Output from the component isn't included.</span></span> <span data-ttu-id="e17dd-136">Cuando se inicia el agente de usuario, Blazor este marcador se usa para arrancar una aplicación.</span><span class="sxs-lookup"><span data-stu-id="e17dd-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e17dd-137">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="e17dd-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="e17dd-138">Aunque las páginas y las vistas pueden usar componentes, la inversa no es verdadera.</span><span class="sxs-lookup"><span data-stu-id="e17dd-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="e17dd-139">Los componentes no pueden usar características específicas de la vista y de la página, como vistas parciales y secciones.</span><span class="sxs-lookup"><span data-stu-id="e17dd-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="e17dd-140">Para utilizar la lógica de una vista parcial en un componente, factorice la lógica de vista parcial en un componente.</span><span class="sxs-lookup"><span data-stu-id="e17dd-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="e17dd-141">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="e17dd-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e17dd-142">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e17dd-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
