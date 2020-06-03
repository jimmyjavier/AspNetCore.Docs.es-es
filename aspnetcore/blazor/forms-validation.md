---
<span data-ttu-id="b7dcc-101">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-103">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-104">'Identity'</span></span>
- <span data-ttu-id="b7dcc-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-106">'Razor'</span></span>
- <span data-ttu-id="b7dcc-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="b7dcc-108">Formularios y validación de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7dcc-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="b7dcc-109">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b7dcc-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b7dcc-110">Blazor admite los formularios y la validación a través de [anotaciones de datos](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="b7dcc-111">El siguiente tipo `ExampleModel` define la lógica de validación a través de anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="b7dcc-112">Un formulario se define mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="b7dcc-113">En el siguiente formulario se reflejan los elementos, componentes y código de Razor típicos:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="b7dcc-114">En el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-114">In the preceding example:</span></span>

* <span data-ttu-id="b7dcc-115">El formulario valida la entrada del usuario en el campo `name` por medio de la validación definida en el tipo `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="b7dcc-116">El modelo se crea en el bloque `@code` del componente y se conserva en un campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="b7dcc-117">El campo se asigna al atributo `Model` del elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="b7dcc-118">El elemento `@bind-Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> enlaza:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="b7dcc-119">La propiedad del modelo (`exampleModel.Name`) a la propiedad `Value` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="b7dcc-120">Para más información sobre el enlace de propiedades, consulte <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="b7dcc-121">Un delegado de evento de cambio a la propiedad `ValueChanged` del componente <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="b7dcc-122">El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="b7dcc-123">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> resume los mensajes de validación.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="b7dcc-124">`HandleValidSubmit` se desencadena cuando el formulario se envía correctamente (supera la validación).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="b7dcc-125">Hay disponible un conjunto de componentes de entrada integrados para recibir y validar las entradas de usuario.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="b7dcc-126">Las entradas se validan cuando se cambian y cuando un formulario se envía.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="b7dcc-127">Los componentes de entrada disponibles se muestran en la siguiente tabla.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="b7dcc-128">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="b7dcc-128">Input component</span></span> | <span data-ttu-id="b7dcc-129">Se representa como&hellip;</span><span class="sxs-lookup"><span data-stu-id="b7dcc-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="b7dcc-130">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-132">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-132">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-133">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-133">'Identity'</span></span>
- <span data-ttu-id="b7dcc-134">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-135">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-135">'Razor'</span></span>
- <span data-ttu-id="b7dcc-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-137">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-139">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-139">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-140">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-140">'Identity'</span></span>
- <span data-ttu-id="b7dcc-141">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-142">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-142">'Razor'</span></span>
- <span data-ttu-id="b7dcc-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-144">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-146">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-146">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-147">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-147">'Identity'</span></span>
- <span data-ttu-id="b7dcc-148">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-149">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-149">'Razor'</span></span>
- <span data-ttu-id="b7dcc-150">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-151">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-153">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-153">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-154">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-154">'Identity'</span></span>
- <span data-ttu-id="b7dcc-155">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-156">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-156">'Razor'</span></span>
- <span data-ttu-id="b7dcc-157">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-158">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-160">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-160">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-161">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-161">'Identity'</span></span>
- <span data-ttu-id="b7dcc-162">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-163">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-163">'Razor'</span></span>
- <span data-ttu-id="b7dcc-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-164">'SignalR' uid:</span></span> 

<span data-ttu-id="b7dcc-165">-------- | --- title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-167">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-167">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-168">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-168">'Identity'</span></span>
- <span data-ttu-id="b7dcc-169">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-170">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-170">'Razor'</span></span>
- <span data-ttu-id="b7dcc-171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-172">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-174">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-174">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-175">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-175">'Identity'</span></span>
- <span data-ttu-id="b7dcc-176">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-177">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-177">'Razor'</span></span>
- <span data-ttu-id="b7dcc-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-179">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-181">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-181">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-182">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-182">'Identity'</span></span>
- <span data-ttu-id="b7dcc-183">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-184">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-184">'Razor'</span></span>
- <span data-ttu-id="b7dcc-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-186">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-188">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-188">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-189">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-189">'Identity'</span></span>
- <span data-ttu-id="b7dcc-190">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-191">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-191">'Razor'</span></span>
- <span data-ttu-id="b7dcc-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-193">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-195">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-195">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-196">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-196">'Identity'</span></span>
- <span data-ttu-id="b7dcc-197">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-198">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-198">'Razor'</span></span>
- <span data-ttu-id="b7dcc-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-200">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-202">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-202">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-203">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-203">'Identity'</span></span>
- <span data-ttu-id="b7dcc-204">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-205">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-205">'Razor'</span></span>
- <span data-ttu-id="b7dcc-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b7dcc-207">title: 'Formularios y validación de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre cómo usar los escenarios de formularios y validación de campos en Blazor.'</span><span class="sxs-lookup"><span data-stu-id="b7dcc-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="b7dcc-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b7dcc-209">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-209">'Blazor'</span></span>
- <span data-ttu-id="b7dcc-210">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-210">'Identity'</span></span>
- <span data-ttu-id="b7dcc-211">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="b7dcc-212">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b7dcc-212">'Razor'</span></span>
- <span data-ttu-id="b7dcc-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-213">'SignalR' uid:</span></span> 

<span data-ttu-id="b7dcc-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="b7dcc-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="b7dcc-215">Todos los componentes de entrada, incluido <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="b7dcc-216">Cualquier atributo que no coincida con un parámetro de componente se agrega al elemento HTML representado.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="b7dcc-217">Los componentes de entrada proporcionan un comportamiento predeterminado de validación al editarse y cambiar su clase CSS para reflejar el estado del campo.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="b7dcc-218">Algunos componentes incluyen lógica de análisis de utilidad.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="b7dcc-219">Así, por ejemplo, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> e <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> tratan los valores no analizables sin problemas registrándolos como errores de validación.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="b7dcc-220">Los tipos con capacidad para aceptar valores nulos también admiten la nulabilidad del campo de destino (por ejemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="b7dcc-221">El siguiente tipo `Starship` define una lógica de validación mediante un conjunto de propiedades y de anotaciones de datos mayor que el del tipo `ExampleModel` anterior:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="b7dcc-222">En el ejemplo anterior, `Description` es opcional porque no hay ninguna anotación de datos presente.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="b7dcc-223">El siguiente formulario valida la entrada de usuario por medio de la validación definida en el modelo `Starship`:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="b7dcc-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> crea un elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> como un [valor en cascada](xref:blazor/components#cascading-values-and-parameters) que lleva un seguimiento de los metadatos del proceso de edición, incluidos los campos que se han modificado y los mensajes de validación actuales.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="b7dcc-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> también proporciona eventos prácticos de envíos válidos y no válidos (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="b7dcc-226">También se puede usar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> para desencadenar la validación y comprobar los valores de campo con código de validación personalizado.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="b7dcc-227">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-227">In the following example:</span></span>

* <span data-ttu-id="b7dcc-228">El método `HandleSubmit` se ejecuta cuando se selecciona el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="b7dcc-229">El formulario se valida con el elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> de dicho formulario.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="b7dcc-230">El formulario se sigue validando pasando <xref:Microsoft.AspNetCore.Components.Forms.EditContext> al método `ServerValidate` que llama a un punto de conexión de API web en el servidor (*no se muestra*).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="b7dcc-231">Se ejecutará más código en función del resultado de la validación del lado cliente y del lado servidor, mediante la comprobación de `isValid`.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="b7dcc-232">InputText basado en el evento de entrada</span><span class="sxs-lookup"><span data-stu-id="b7dcc-232">InputText based on the input event</span></span>

<span data-ttu-id="b7dcc-233">Use el componente <xref:Microsoft.AspNetCore.Components.Forms.InputText> para crear un componente personalizado que usa el evento `input` en lugar del evento `change`.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="b7dcc-234">Cree un componente con el siguiente marcado y use dicho componente igual que <xref:Microsoft.AspNetCore.Components.Forms.InputText>:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="b7dcc-235">Trabajo con botones de radio</span><span class="sxs-lookup"><span data-stu-id="b7dcc-235">Work with radio buttons</span></span>

<span data-ttu-id="b7dcc-236">Al trabajar con botones de radio en un formulario, el enlace de datos se controla de manera diferente que otros elementos, ya que los botones de radio se evalúan como un grupo.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="b7dcc-237">El valor de cada botón de radio es fijo, pero el valor del grupo de botones de radio es el valor del botón de radio seleccionado.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="b7dcc-238">El ejemplo siguiente muestra cómo:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-238">The following example shows how to:</span></span>

* <span data-ttu-id="b7dcc-239">Controlar el enlace de datos en un grupo de botones de radio</span><span class="sxs-lookup"><span data-stu-id="b7dcc-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="b7dcc-240">Admitir la validación usando un componente `InputRadio` personalizado</span><span class="sxs-lookup"><span data-stu-id="b7dcc-240">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="b7dcc-241">El siguiente elemento <xref:Microsoft.AspNetCore.Components.Forms.EditForm> usa el componente `InputRadio` anterior para obtener y validar una clasificación del usuario:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="b7dcc-242">Compatibilidad con la validación</span><span class="sxs-lookup"><span data-stu-id="b7dcc-242">Validation support</span></span>

<span data-ttu-id="b7dcc-243">El componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> adjunta la compatibilidad con la validación mediante anotaciones de datos al elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> en cascada.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="b7dcc-244">Este gesto explícito es necesario para habilitar la compatibilidad con la validación mediante anotaciones.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="b7dcc-245">Para usar un sistema de validación distinto al de las anotaciones de datos, reemplace <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> por una implementación personalizada.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="b7dcc-246">La implementación de ASP.NET Core está disponible para su inspección en el origen de referencia: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="b7dcc-247"> realiza dos tipos de validación:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-247"> performs two types of validation:</span></span>

* <span data-ttu-id="b7dcc-248">La *validación del campo* se realiza cuando el usuario sale de un campo usando la tecla TAB.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="b7dcc-249">Durante una validación del campo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> asocia al campo todos los resultados de validación notificados.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="b7dcc-250">La *validación del modelo* se realiza cuando el usuario envía el formulario.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="b7dcc-251">Durante una validación del modelo, el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> intenta determinar el campo en función del nombre del miembro que se indica en el resultado de la validación.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="b7dcc-252">Los resultados de validación que no están asociados a un miembro individual se asocian al modelo en lugar de a un campo.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="b7dcc-253">Resumen de validación y componentes de los mensajes de validación</span><span class="sxs-lookup"><span data-stu-id="b7dcc-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="b7dcc-254">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>, que resume todos los mensajes de validación, es similar a la [aplicación auxiliar de etiquetas ValidationSummary](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="b7dcc-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="b7dcc-255">Mensajes de validación de salida de un modelo específico con el parámetro `Model`:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="b7dcc-256">El componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>, que muestra los mensajes de validación de un campo específico, es similar a la [aplicación auxiliar de etiquetas Validation Message](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="b7dcc-257">Especifique el campo de validación con el atributo `For` y una expresión lambda donde se indique la propiedad del modelo:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="b7dcc-258">Los componentes <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> y <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> admiten atributos arbitrarios.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="b7dcc-259">Cualquier atributo que no coincida con un parámetro de componente se agrega a los elementos `<div>` o `<ul>` generados.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="b7dcc-260">Atributos de validación personalizados</span><span class="sxs-lookup"><span data-stu-id="b7dcc-260">Custom validation attributes</span></span>

<span data-ttu-id="b7dcc-261">Para asegurarnos de que un resultado de validación está correctamente asociado a un campo cuando se usa un [atributo de validación personalizado](xref:mvc/models/validation#custom-attributes), pase el elemento <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> del contexto de validación al crear el elemento <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="b7dcc-262">Paquete de validación de anotaciones de datos de Blazor</span><span class="sxs-lookup"><span data-stu-id="b7dcc-262">Blazor data annotations validation package</span></span>

<span data-ttu-id="b7dcc-263">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) es un paquete que llena los huecos de la experiencia de validación mediante el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="b7dcc-264">Actualmente, el paquete está en fase *experimental*.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="b7dcc-265">Atributo [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="b7dcc-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="b7dcc-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> no funciona bien con el componente <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> porque no asocia el resultado de la validación a un miembro específico.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="b7dcc-267">Esto puede generar un comportamiento incoherente entre la validación de nivel de campo y el momento en que el modelo completo se valida al enviarse.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="b7dcc-268">El paquete *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) incluye un atributo de validación más, `ComparePropertyAttribute`, que pone fin a estas limitaciones.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="b7dcc-269">En una aplicación Blazor, `[CompareProperty]` es un reemplazo directo del atributo [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="b7dcc-270">Tipos de colección, tipos complejos y modelos anidados</span><span class="sxs-lookup"><span data-stu-id="b7dcc-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="b7dcc-271"> proporciona compatibilidad para validar la entrada del formulario mediante anotaciones de datos con el elemento integrado <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>,</span><span class="sxs-lookup"><span data-stu-id="b7dcc-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="b7dcc-272">pero <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> solo valida las propiedades de nivel superior del modelo enlazadas al formulario que no son propiedades de tipos complejos o de colección.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="b7dcc-273">Para validar el gráfico de objetos completo del modelo enlazado, incluidas las propiedades de tipos complejos o de colección, use el elemento `ObjectGraphDataAnnotationsValidator` proporcionado por el paquete *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):</span><span class="sxs-lookup"><span data-stu-id="b7dcc-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="b7dcc-274">Anote las propiedades del modelo con `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="b7dcc-275">En las siguientes clases de modelo, la clase `ShipDescription` contiene más anotaciones de datos para validar cuando el modelo está enlazado al formulario:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="b7dcc-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-276">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="b7dcc-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-277">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="b7dcc-278">Habilitar el botón Enviar según la validación del formulario</span><span class="sxs-lookup"><span data-stu-id="b7dcc-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="b7dcc-279">Para habilitar el botón Enviar según la validación del formulario:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="b7dcc-280">Use el elemento <xref:Microsoft.AspNetCore.Components.Forms.EditContext> del formulario para asignar el modelo cuando el componente se inicialice.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="b7dcc-281">Valide el formulario en la devolución de llamada <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> del contexto para habilitar y deshabilitar el botón Enviar.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="b7dcc-282">Desenlace el controlador de eventos en el método `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="b7dcc-283">Para obtener más información, vea <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="b7dcc-284">En el ejemplo anterior, establezca `formInvalid` en `false` en los siguientes casos:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="b7dcc-285">El formulario se carga previamente con valores predeterminados válidos.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="b7dcc-286">Quiere habilitar el botón Enviar cuando el formulario se cargue.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="b7dcc-287">Un efecto secundario del método anterior es que un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> se rellena con campos no válidos después de que el usuario interactúe con algún campo.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="b7dcc-288">Este escenario se puede abordar de cualquiera de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="b7dcc-289">No use un componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> en el formulario.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="b7dcc-290">Haga que el componente <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> esté visible cuando se seleccione el botón Enviar (por ejemplo, en un método `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="b7dcc-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="b7dcc-291">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b7dcc-291">Troubleshoot</span></span>

> <span data-ttu-id="b7dcc-292">InvalidOperationException: EditForm requiere un parámetro Model o un parámetro EditContext, pero no ambos.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-292">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="b7dcc-293">Confirme que <xref:Microsoft.AspNetCore.Components.Forms.EditForm> tiene un parámetro <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> o <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="b7dcc-293">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="b7dcc-294">Al asignar un parámetro <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> al formulario, confirme que se crea una instancia del tipo de modelo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b7dcc-294">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
