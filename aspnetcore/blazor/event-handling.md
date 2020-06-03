---
<span data-ttu-id="f5e4d-101">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-103">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-104">'Identity'</span></span>
- <span data-ttu-id="f5e4d-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-106">'Razor'</span></span>
- <span data-ttu-id="f5e4d-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="f5e4d-108">Control de eventos de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5e4d-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="f5e4d-109">Por [Luke Latham](https://github.com/guardrex) y [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f5e4d-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f5e4d-110">Los componentes de Razor proporcionan características de control de eventos.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-110">Razor components provide event handling features.</span></span> <span data-ttu-id="f5e4d-111">Para un atributo de elemento HTML denominado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por ejemplo, `@onclick`) con un valor de tipo delegado, el componente de Razor trata el valor del atributo como un controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="f5e4d-112">El código siguiente llama al método `UpdateHeading` cuando se selecciona el botón en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="f5e4d-113">El código siguiente llama al método `CheckChanged` cuando se cambia la casilla en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="f5e4d-114">Los controladores de eventos también pueden ser asincrónicos y devolver un objeto <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="f5e4d-115">No es necesario llamar a [StateHasChanged](xref:blazor/lifecycle#state-changes) de forma manual.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="f5e4d-116">Las excepciones se registran cuando se producen.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="f5e4d-117">En el ejemplo siguiente, se llama a `UpdateHeading` de forma asincrónica cuando se selecciona el botón:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="f5e4d-118">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="f5e4d-118">Event argument types</span></span>

<span data-ttu-id="f5e4d-119">En algunos eventos, se permiten los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="f5e4d-120">Solo es necesario especificar un tipo de evento en la llamada de método si el tipo de evento se usa en el método.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="f5e4d-121">En la tabla siguiente se muestran los valores <xref:System.EventArgs> admitidos.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="f5e4d-122">evento</span><span class="sxs-lookup"><span data-stu-id="f5e4d-122">Event</span></span>            | <span data-ttu-id="f5e4d-123">Clase</span><span class="sxs-lookup"><span data-stu-id="f5e4d-123">Class</span></span>                | <span data-ttu-id="f5e4d-124">Eventos y notas de DOM</span><span class="sxs-lookup"><span data-stu-id="f5e4d-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="f5e4d-125">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-127">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-127">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-128">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-128">'Identity'</span></span>
- <span data-ttu-id="f5e4d-129">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-130">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-130">'Razor'</span></span>
- <span data-ttu-id="f5e4d-131">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-132">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-134">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-134">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-135">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-135">'Identity'</span></span>
- <span data-ttu-id="f5e4d-136">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-137">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-137">'Razor'</span></span>
- <span data-ttu-id="f5e4d-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-139">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-141">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-141">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-142">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-142">'Identity'</span></span>
- <span data-ttu-id="f5e4d-143">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-144">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-144">'Razor'</span></span>
- <span data-ttu-id="f5e4d-145">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-146">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-148">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-148">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-149">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-149">'Identity'</span></span>
- <span data-ttu-id="f5e4d-150">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-151">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-151">'Razor'</span></span>
- <span data-ttu-id="f5e4d-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-153">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-155">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-155">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-156">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-156">'Identity'</span></span>
- <span data-ttu-id="f5e4d-157">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-158">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-158">'Razor'</span></span>
- <span data-ttu-id="f5e4d-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-160">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-162">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-162">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-163">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-163">'Identity'</span></span>
- <span data-ttu-id="f5e4d-164">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-165">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-165">'Razor'</span></span>
- <span data-ttu-id="f5e4d-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-166">'SignalR' uid:</span></span> 

<span data-ttu-id="f5e4d-167">-------- | --- title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-169">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-169">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-170">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-170">'Identity'</span></span>
- <span data-ttu-id="f5e4d-171">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-172">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-172">'Razor'</span></span>
- <span data-ttu-id="f5e4d-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-174">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-176">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-176">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-177">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-177">'Identity'</span></span>
- <span data-ttu-id="f5e4d-178">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-179">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-179">'Razor'</span></span>
- <span data-ttu-id="f5e4d-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-181">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-183">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-183">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-184">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-184">'Identity'</span></span>
- <span data-ttu-id="f5e4d-185">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-186">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-186">'Razor'</span></span>
- <span data-ttu-id="f5e4d-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-188">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-190">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-190">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-191">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-191">'Identity'</span></span>
- <span data-ttu-id="f5e4d-192">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-193">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-193">'Razor'</span></span>
- <span data-ttu-id="f5e4d-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-195">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-197">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-197">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-198">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-198">'Identity'</span></span>
- <span data-ttu-id="f5e4d-199">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-200">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-200">'Razor'</span></span>
- <span data-ttu-id="f5e4d-201">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-202">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-204">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-204">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-205">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-205">'Identity'</span></span>
- <span data-ttu-id="f5e4d-206">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-207">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-207">'Razor'</span></span>
- <span data-ttu-id="f5e4d-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-209">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-211">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-211">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-212">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-212">'Identity'</span></span>
- <span data-ttu-id="f5e4d-213">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-214">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-214">'Razor'</span></span>
- <span data-ttu-id="f5e4d-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-216">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-218">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-218">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-219">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-219">'Identity'</span></span>
- <span data-ttu-id="f5e4d-220">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-221">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-221">'Razor'</span></span>
- <span data-ttu-id="f5e4d-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-222">'SignalR' uid:</span></span> 

<span data-ttu-id="f5e4d-223">---------- | --- title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-225">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-225">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-226">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-226">'Identity'</span></span>
- <span data-ttu-id="f5e4d-227">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-228">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-228">'Razor'</span></span>
- <span data-ttu-id="f5e4d-229">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-230">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-232">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-232">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-233">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-233">'Identity'</span></span>
- <span data-ttu-id="f5e4d-234">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-235">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-235">'Razor'</span></span>
- <span data-ttu-id="f5e4d-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-237">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-239">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-239">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-240">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-240">'Identity'</span></span>
- <span data-ttu-id="f5e4d-241">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-242">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-242">'Razor'</span></span>
- <span data-ttu-id="f5e4d-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-244">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-246">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-246">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-247">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-247">'Identity'</span></span>
- <span data-ttu-id="f5e4d-248">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-249">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-249">'Razor'</span></span>
- <span data-ttu-id="f5e4d-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-251">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-253">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-253">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-254">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-254">'Identity'</span></span>
- <span data-ttu-id="f5e4d-255">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-256">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-256">'Razor'</span></span>
- <span data-ttu-id="f5e4d-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-258">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-260">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-260">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-261">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-261">'Identity'</span></span>
- <span data-ttu-id="f5e4d-262">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-263">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-263">'Razor'</span></span>
- <span data-ttu-id="f5e4d-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-265">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-267">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-267">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-268">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-268">'Identity'</span></span>
- <span data-ttu-id="f5e4d-269">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-270">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-270">'Razor'</span></span>
- <span data-ttu-id="f5e4d-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5e4d-272">title: 'Control de eventos de Blazor en ASP.NET Core' author: description: 'Obtenga información sobre las características de control de eventos de Blazor, incluidos los tipos de argumento de evento, las devoluciones de llamadas de eventos y la administración de eventos predeterminados del explorador.'</span><span class="sxs-lookup"><span data-stu-id="f5e4d-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="f5e4d-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5e4d-274">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-274">'Blazor'</span></span>
- <span data-ttu-id="f5e4d-275">"Identity"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-275">'Identity'</span></span>
- <span data-ttu-id="f5e4d-276">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5e4d-277">"Razor"</span><span class="sxs-lookup"><span data-stu-id="f5e4d-277">'Razor'</span></span>
- <span data-ttu-id="f5e4d-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-278">'SignalR' uid:</span></span> 

<span data-ttu-id="f5e4d-279">---------- | | Portapapeles        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Arrastrar             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="f5e4d-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="f5e4d-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> y <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contienen datos de elementos arrastrados.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="f5e4d-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Evento            | <xref:System.EventArgs> | *General*</span><span class="sxs-lookup"><span data-stu-id="f5e4d-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="f5e4d-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="f5e4d-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="f5e4d-283">*Portapapeles*</span><span class="sxs-lookup"><span data-stu-id="f5e4d-283">*Clipboard*</span></span><br><span data-ttu-id="f5e4d-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="f5e4d-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="f5e4d-285">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="f5e4d-285">*Input*</span></span><br><span data-ttu-id="f5e4d-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="f5e4d-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="f5e4d-287">*Elementos multimedia*</span><span class="sxs-lookup"><span data-stu-id="f5e4d-287">*Media*</span></span><br><span data-ttu-id="f5e4d-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="f5e4d-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="f5e4d-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> contiene atributos para configurar las asignaciones entre los nombres de evento y los tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="f5e4d-290">| | Foco | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="f5e4d-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="f5e4d-291">No incluye compatibilidad con `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="f5e4d-292">| | Entrada            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Teclado         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Puntero del mouse    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Rueda del mouse      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progreso         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Táctil            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="f5e4d-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="f5e4d-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> representa un único punto de contacto en un dispositivo sensible al tacto.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="f5e4d-294">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="f5e4d-295">[Clases EventArgs en el origen de referencia de ASP.NET Core (dotnet/versión de aspnetcore/rama 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="f5e4d-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="f5e4d-296">[Documentación web de MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): incluye información sobre qué elementos HTML admite cada evento DOM.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="f5e4d-297">Expresiones lambda</span><span class="sxs-lookup"><span data-stu-id="f5e4d-297">Lambda expressions</span></span>

<span data-ttu-id="f5e4d-298">También se pueden usar [expresiones lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="f5e4d-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="f5e4d-299">A menudo resulta cómodo cerrar los valores adicionales, como al recorrer en iteración un conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="f5e4d-300">En el ejemplo siguiente se crean tres botones: cada uno llama a `UpdateHeading` y pasa un argumento de evento (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) y su número de botón (`buttonNumber`) cuando se selecciona en la interfaz de usuario:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="f5e4d-301">**No** use la variable de bucle (`i`) en un bucle `for` directamente en una expresión lambda.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="f5e4d-302">De lo contrario, todas las expresiones lambda usan la misma variable, lo que hace que el valor de `i` sea el mismo en todas las expresiones lambda.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="f5e4d-303">Capture siempre su valor en una variable local (`buttonNumber` en el ejemplo anterior) y, después, úsela.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="f5e4d-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="f5e4d-304">EventCallback</span></span>

<span data-ttu-id="f5e4d-305">Un escenario común con los componentes anidados es el deseo de ejecutar el método de un componente primario cuando se produce un evento de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="f5e4d-306">Un caso habitual es un evento `onclick` que se produce en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="f5e4d-307">Para exponer eventos entre componentes, use un elemento <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="f5e4d-308">Un componente primario puede asignar un método de devolución de llamada al elemento <xref:Microsoft.AspNetCore.Components.EventCallback> de un componente secundario.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="f5e4d-309">El elemento `ChildComponent` de la aplicación de ejemplo (*Components/ChildComponent.razor*) muestra cómo se configura el controlador `onclick` de un botón para recibir un delegado de <xref:Microsoft.AspNetCore.Components.EventCallback> del objeto `ParentComponent` del ejemplo.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="f5e4d-310">El elemento <xref:Microsoft.AspNetCore.Components.EventCallback> tiene el tipo `MouseEventArgs`, que es adecuado para un evento `onclick` desde un dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="f5e4d-311">`ParentComponent` establece el objeto <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) del elemento secundario en su método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="f5e4d-312">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-312">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="f5e4d-313">Cuando el botón se selecciona en `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="f5e4d-314">Se llama al método `ShowMessage` de `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="f5e4d-315">`messageText` se actualiza y se muestra en `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="f5e4d-316">No se requiere una llamada a [StateHasChanged](xref:blazor/lifecycle#state-changes) en el método de la devolución de llamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="f5e4d-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="f5e4d-317">Se llama a <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> de forma automática para volver a representar el elemento `ParentComponent`, del mismo modo que los eventos secundarios desencadenan la nueva representación de los componentes en los controladores de eventos que se ejecutan dentro del elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="f5e4d-318"><xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> permiten delegados asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="f5e4d-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> está fuertemente tipado y requiere un tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="f5e4d-320"><xref:Microsoft.AspNetCore.Components.EventCallback> está débilmente tipado y permite cualquier tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="f5e4d-321">Invoque <xref:Microsoft.AspNetCore.Components.EventCallback> o <xref:Microsoft.AspNetCore.Components.EventCallback%601> con <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> y espere a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="f5e4d-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> y <xref:Microsoft.AspNetCore.Components.EventCallback%601> para el control de eventos y el enlace de parámetros de componentes.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="f5e4d-323">Se prefiere <xref:Microsoft.AspNetCore.Components.EventCallback%601> (fuertemente tipado) a <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="f5e4d-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> proporciona mejores comentarios de errores a los usuarios del componente.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="f5e4d-325">Como sucede con otros controladores de eventos de la interfaz de usuario, la especificación del parámetro de evento es opcional.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="f5e4d-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> cuando no se pase ningún valor a la devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="f5e4d-327">Bloqueo de acciones predeterminadas</span><span class="sxs-lookup"><span data-stu-id="f5e4d-327">Prevent default actions</span></span>

<span data-ttu-id="f5e4d-328">Use el atributo de directiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para impedir la acción predeterminada de un evento.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="f5e4d-329">Si se selecciona una tecla en un dispositivo de entrada y el foco del elemento está en un cuadro de texto, un explorador muestra normalmente el carácter de la tecla en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="f5e4d-330">En el ejemplo siguiente, el comportamiento predeterminado se evita mediante la especificación del atributo de directiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="f5e4d-331">El contador se incrementa y la tecla **+** no se captura en el valor del elemento `<input>`:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="f5e4d-332">Especificar el atributo `@on{EVENT}:preventDefault` sin un valor es equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="f5e4d-333">El valor del atributo también puede ser una expresión.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="f5e4d-334">En el ejemplo siguiente, `shouldPreventDefault` es un campo `bool` establecido en `true` o `false`:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="f5e4d-335">No se necesita un controlador de eventos para impedir la acción predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="f5e4d-336">El controlador de eventos y el bloqueo de escenarios de acción predeterminados se pueden usar de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="f5e4d-337">Detención de la propagación de eventos</span><span class="sxs-lookup"><span data-stu-id="f5e4d-337">Stop event propagation</span></span>

<span data-ttu-id="f5e4d-338">Use el atributo de directiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para detener la propagación de eventos.</span><span class="sxs-lookup"><span data-stu-id="f5e4d-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="f5e4d-339">En el ejemplo siguiente, al activar la casilla se impide que los eventos de clic del elemento secundario `<div>` se propaguen al elemento principal `<div>`:</span><span class="sxs-lookup"><span data-stu-id="f5e4d-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
