---
<span data-ttu-id="d992c-101">title: "Llamada a funciones de JavaScript con métodos .NET en ASP.NET Core Blazor" author: description: "Aprenda a invocar funciones de JavaScript con métodos .NET en aplicaciones Blazor".</span><span class="sxs-lookup"><span data-stu-id="d992c-101">title: 'Call JavaScript functions from .NET methods in ASP.NET Core Blazor' author: description: 'Learn how to invoke JavaScript functions from .NET methods in Blazor apps.'</span></span>
<span data-ttu-id="d992c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d992c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d992c-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d992c-103">'Blazor'</span></span>
- <span data-ttu-id="d992c-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d992c-104">'Identity'</span></span>
- <span data-ttu-id="d992c-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d992c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d992c-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d992c-106">'Razor'</span></span>
- <span data-ttu-id="d992c-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d992c-107">'SignalR' uid:</span></span> 

---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="d992c-108">Llamada a funciones de JavaScript con métodos de .NET en Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d992c-108">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="d992c-109">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d992c-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d992c-110">Una aplicación de Blazor puede invocar funciones de JavaScript desde métodos de .NET y viceversa.</span><span class="sxs-lookup"><span data-stu-id="d992c-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="d992c-111">Estos escenarios se denominan *interoperabilidad de JavaScript* (o *interoperabilidad de JS*).</span><span class="sxs-lookup"><span data-stu-id="d992c-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="d992c-112">En este artículo se describe cómo invocar funciones de JavaScript desde .NET.</span><span class="sxs-lookup"><span data-stu-id="d992c-112">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="d992c-113">Para más información sobre cómo llamar a métodos de .NET desde JavaScript, vea <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="d992c-113">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="d992c-114">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d992c-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d992c-115">Para llamar a JavaScript desde .NET, use la abstracción `IJSRuntime`.</span><span class="sxs-lookup"><span data-stu-id="d992c-115">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="d992c-116">Para emitir llamadas de interoperabilidad de JS, inserte la abstracción `IJSRuntime` en el componente.</span><span class="sxs-lookup"><span data-stu-id="d992c-116">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="d992c-117">El método `InvokeAsync<T>` toma un identificador de la función de JavaScript que queremos invocar junto con un número cualquiera de argumentos serializables con JSON.</span><span class="sxs-lookup"><span data-stu-id="d992c-117">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="d992c-118">El identificador de función es relativo al ámbito global (`window`).</span><span class="sxs-lookup"><span data-stu-id="d992c-118">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="d992c-119">Si quiere llamar a `window.someScope.someFunction`, el identificador es `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="d992c-119">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="d992c-120">No es necesario registrar la función para poder llamarla.</span><span class="sxs-lookup"><span data-stu-id="d992c-120">There's no need to register the function before it's called.</span></span> <span data-ttu-id="d992c-121">El tipo de valor devuelto `T` también debe ser serializable con JSON.</span><span class="sxs-lookup"><span data-stu-id="d992c-121">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="d992c-122">`T` debe coincidir con el tipo de .NET que mejor asignación tenga con el tipo de JSON devuelto.</span><span class="sxs-lookup"><span data-stu-id="d992c-122">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="d992c-123">En el caso de las aplicaciones de servidor Blazor que tienen habilitada la representación previa, no se puede llamar a JavaScript durante la representación previa inicial.</span><span class="sxs-lookup"><span data-stu-id="d992c-123">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="d992c-124">Las llamadas de interoperabilidad de JavaScript se deben aplazar hasta que se establezca la conexión con el explorador.</span><span class="sxs-lookup"><span data-stu-id="d992c-124">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="d992c-125">Para más información, vea la sección [Detección de cuándo se está obteniendo una representación previa de una aplicación de servidor Blazor](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="d992c-125">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="d992c-126">El siguiente ejemplo se basa en [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), un descodificador basado en JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d992c-126">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="d992c-127">En él se explica cómo invocar una función de JavaScript desde un método de C#.</span><span class="sxs-lookup"><span data-stu-id="d992c-127">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="d992c-128">La función de JavaScript acepta una matriz de bytes procedente de un método de C#, la descodifica y devuelve el texto al componente para que lo muestre.</span><span class="sxs-lookup"><span data-stu-id="d992c-128">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="d992c-129">Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `TextDecoder` para descodificar una matriz que se ha pasado y devolver el valor descodificado:</span><span class="sxs-lookup"><span data-stu-id="d992c-129">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="d992c-130">El código de JavaScript, como el código que se muestra en el ejemplo anterior, también se puede cargar desde un archivo JavaScript ( *.js*) con una referencia al archivo de script:</span><span class="sxs-lookup"><span data-stu-id="d992c-130">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="d992c-131">El siguiente componente:</span><span class="sxs-lookup"><span data-stu-id="d992c-131">The following component:</span></span>

* <span data-ttu-id="d992c-132">Invoca la función de JavaScript `convertArray` mediante `JSRuntime` cuando se selecciona un botón de componente (**Convert Array**).</span><span class="sxs-lookup"><span data-stu-id="d992c-132">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="d992c-133">Después de llamar a la función de JavaScript, la matriz que se ha pasado se convierte en una cadena,</span><span class="sxs-lookup"><span data-stu-id="d992c-133">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="d992c-134">que se devuelve al componente para que la muestre.</span><span class="sxs-lookup"><span data-stu-id="d992c-134">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="d992c-135">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="d992c-135">IJSRuntime</span></span>

<span data-ttu-id="d992c-136">Para usar la abstracción `IJSRuntime`, adopte cualquiera de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="d992c-136">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="d992c-137">Inserte la abstracción `IJSRuntime` en el componente de Razor ( *.razor*):</span><span class="sxs-lookup"><span data-stu-id="d992c-137">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="d992c-138">Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="d992c-138">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d992c-139">Se llama a la función con `IJSRuntime.InvokeVoidAsync` y no se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="d992c-139">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="d992c-140">Inyecte la abstracción `IJSRuntime` en una clase ( *.cs*):</span><span class="sxs-lookup"><span data-stu-id="d992c-140">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="d992c-141">Dentro del elemento `<head>` de *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor), proporcione una función de JavaScript que use `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="d992c-141">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="d992c-142">Se llama a la función con `JSRuntime.InvokeAsync` y sí se devuelve un valor:</span><span class="sxs-lookup"><span data-stu-id="d992c-142">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="d992c-143">Para generar contenido dinámico con [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use el atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="d992c-143">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="d992c-144">En la aplicación de ejemplo del lado cliente de este tema hay dos funciones de JavaScript disponibles para la aplicación que interactúan con el DOM para recibir los datos proporcionados por el usuario y mostrar un mensaje de bienvenida:</span><span class="sxs-lookup"><span data-stu-id="d992c-144">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="d992c-145">`showPrompt`: genera un mensaje para aceptar la entrada del usuario (el nombre del usuario) y devuelve dicho nombre al autor de la llamada.</span><span class="sxs-lookup"><span data-stu-id="d992c-145">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="d992c-146">`displayWelcome`: asigna un mensaje de bienvenida del autor de la llamada a un objeto DOM con un `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="d992c-146">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="d992c-147">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="d992c-147">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="d992c-148">Coloque la etiqueta `<script>` que hace referencia al archivo JavaScript en el archivo *wwwroot/index.html* (WebAssembly de Blazor) o *Pages/_Host.cshtml* (servidor Blazor).</span><span class="sxs-lookup"><span data-stu-id="d992c-148">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="d992c-149">*wwwroot/index.html* (WebAssembly de Blazor):</span><span class="sxs-lookup"><span data-stu-id="d992c-149">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="d992c-150">*Pages/_Host.cshtml* (servidor Blazor):</span><span class="sxs-lookup"><span data-stu-id="d992c-150">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="d992c-151">No coloque una etiqueta `<script>` en un archivo de componente, porque la etiqueta `<script>` no se puede actualizar dinámicamente.</span><span class="sxs-lookup"><span data-stu-id="d992c-151">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="d992c-152">Los métodos de .NET interoperan con las funciones de JavaScript en el archivo *exampleJsInterop.js* llamando a `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="d992c-152">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="d992c-153">La abstracción `IJSRuntime` es asincrónica para dar cabida a escenarios de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="d992c-153">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="d992c-154">Si la aplicación es una aplicación de WebAssembly de Blazor y quiere invocar una función de JavaScript sincrónicamente, conviértala a un tipo heredado `IJSInProcessRuntime` y llame a `Invoke<T>` en su lugar.</span><span class="sxs-lookup"><span data-stu-id="d992c-154">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="d992c-155">Se recomienda que la mayoría de las bibliotecas de interoperabilidad de JS usen API asincrónicas para garantizar que esas bibliotecas van a estar disponibles en todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="d992c-155">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="d992c-156">La aplicación de ejemplo incluye un componente para mostrar la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="d992c-156">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="d992c-157">El componente:</span><span class="sxs-lookup"><span data-stu-id="d992c-157">The component:</span></span>

* <span data-ttu-id="d992c-158">Recibe la entrada del usuario a través de un mensaje de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d992c-158">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="d992c-159">Devuelve el texto al componente para que lo procese.</span><span class="sxs-lookup"><span data-stu-id="d992c-159">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="d992c-160">Llama a una segunda función de JavaScript que interactúa con el DOM para mostrar un mensaje de bienvenida.</span><span class="sxs-lookup"><span data-stu-id="d992c-160">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="d992c-161">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="d992c-161">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="d992c-162">Cuando `TriggerJsPrompt` se ejecuta seleccionando el botón **Trigger JavaScript Prompt** del componente, se llama a la función `showPrompt` de JavaScript proporcionada en el archivo *wwwroot/exampleJsInterop.js*.</span><span class="sxs-lookup"><span data-stu-id="d992c-162">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="d992c-163">La función `showPrompt` acepta la entrada del usuario (el nombre del usuario), que se codifica en HTML y se devuelve al componente.</span><span class="sxs-lookup"><span data-stu-id="d992c-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="d992c-164">El componente almacena el nombre del usuario en una variable local, `name`.</span><span class="sxs-lookup"><span data-stu-id="d992c-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="d992c-165">La cadena almacenada en `name` se incorpora a un mensaje de bienvenida, que se pasa a una función de JavaScript, `displayWelcome`, que representa el mensaje de bienvenida en una etiqueta de encabezado.</span><span class="sxs-lookup"><span data-stu-id="d992c-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="d992c-166">Llamada a una función de JavaScript vacía</span><span class="sxs-lookup"><span data-stu-id="d992c-166">Call a void JavaScript function</span></span>

<span data-ttu-id="d992c-167">Las funciones de JavaScript que devuelven [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) o [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) se llaman con `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="d992c-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="d992c-168">Detección de cuándo se está obteniendo una representación previa de una aplicación de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="d992c-168">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="d992c-169">Captura de referencias a elementos</span><span class="sxs-lookup"><span data-stu-id="d992c-169">Capture references to elements</span></span>

<span data-ttu-id="d992c-170">Algunos escenarios de interoperabilidad de JS requieren referencias a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="d992c-170">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="d992c-171">Por ejemplo, una biblioteca de interfaz de usuario puede requerir una referencia de elemento para inicializarse, o puede que necesite llamar a API de tipo comando en un elemento, como `focus` o `play`.</span><span class="sxs-lookup"><span data-stu-id="d992c-171">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="d992c-172">Use el siguiente método para capturar referencias a elementos HTML en un componente:</span><span class="sxs-lookup"><span data-stu-id="d992c-172">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="d992c-173">Agregue un atributo `@ref` al elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="d992c-173">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="d992c-174">Defina un campo de tipo `ElementReference` cuyo nombre coincida con el valor del atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="d992c-174">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="d992c-175">En el siguiente ejemplo se muestra la captura de una referencia al elemento `<input>` `username`:</span><span class="sxs-lookup"><span data-stu-id="d992c-175">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="d992c-176">Use únicamente una referencia de elemento para mutar el contenido de un elemento vacío que no interactúa con Blazor.</span><span class="sxs-lookup"><span data-stu-id="d992c-176">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="d992c-177">Este escenario es útil cuando una API de terceros proporciona contenido al elemento.</span><span class="sxs-lookup"><span data-stu-id="d992c-177">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="d992c-178">Dado que Blazor no interactúa con el elemento, no existe ninguna posibilidad de que se produzca un conflicto entre la representación de Blazor del elemento y el DOM.</span><span class="sxs-lookup"><span data-stu-id="d992c-178">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="d992c-179">En el siguiente ejemplo, es *peligroso* mutar el contenido de la lista sin ordenar (`ul`) porque Blazor interactúa con el DOM para rellenar los elementos de lista de este elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="d992c-179">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="d992c-180">Si la interoperabilidad de JS muta el contenido del elemento `MyList` y Blazor intenta realizar comparaciones con ese elemento, las comparaciones no coincidirán con el DOM.</span><span class="sxs-lookup"><span data-stu-id="d992c-180">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="d992c-181">En lo que respecta al código .NET, un elemento `ElementReference` es un identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="d992c-181">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="d992c-182">*Lo único* que se puede hacer con `ElementReference` es pasarlo a código de JavaScript a través de la interoperabilidad de JS.</span><span class="sxs-lookup"><span data-stu-id="d992c-182">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="d992c-183">Al hacerlo, el código del lado JavaScript recibe una instancia de `HTMLElement`, que puede usar con las API de DOM habituales.</span><span class="sxs-lookup"><span data-stu-id="d992c-183">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="d992c-184">Por ejemplo, el siguiente código define un método de extensión de .NET que permite establecer el foco en un elemento:</span><span class="sxs-lookup"><span data-stu-id="d992c-184">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="d992c-185">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="d992c-185">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="d992c-186">Para llamar a una función de JavaScript que no devuelve un valor, use `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="d992c-186">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="d992c-187">El siguiente código establece el foco en la entrada de nombre de usuario llamando a la función de JavaScript anterior con el elemento `ElementReference` capturado:</span><span class="sxs-lookup"><span data-stu-id="d992c-187">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="d992c-188">Para usar un método de extensión, cree un método de extensión estático que reciba la instancia de `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="d992c-188">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="d992c-189">Se llama al método `Focus` directamente en el objeto.</span><span class="sxs-lookup"><span data-stu-id="d992c-189">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="d992c-190">En el siguiente ejemplo se da por hecho que el método `Focus` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="d992c-190">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="d992c-191">La variable `username` solo se rellena después de que el componente se represente.</span><span class="sxs-lookup"><span data-stu-id="d992c-191">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="d992c-192">Si se pasa un elemento `ElementReference` sin rellenar al código de JavaScript, el código de JavaScript recibe un valor `null`.</span><span class="sxs-lookup"><span data-stu-id="d992c-192">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="d992c-193">Para manipular referencias de elemento una vez finalizada la representación del componente (para establecer el foco inicial en un elemento), use los [métodos de ciclo de vida del componente OnAfterRenderAsync u OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="d992c-193">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="d992c-194">Cuando se trabaje con tipos genéricos y se devuelva un valor, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="d992c-194">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="d992c-195">Se llama al método `GenericMethod` directamente en el objeto con un tipo.</span><span class="sxs-lookup"><span data-stu-id="d992c-195">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="d992c-196">En el siguiente ejemplo se da por hecho que el método `GenericMethod` está disponible en el espacio de nombres `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="d992c-196">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="d992c-197">Referencia a elementos en componentes</span><span class="sxs-lookup"><span data-stu-id="d992c-197">Reference elements across components</span></span>

<span data-ttu-id="d992c-198">La validez de un elemento `ElementReference` solo está garantizada en el método de `OnAfterRender` de un componente (y una referencia de elemento es un `struct`), por lo que una referencia de elemento no se puede pasar entre componentes.</span><span class="sxs-lookup"><span data-stu-id="d992c-198">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="d992c-199">Para que un componente primario haga que una referencia de elemento esté disponible para otros componentes, el componente primario puede:</span><span class="sxs-lookup"><span data-stu-id="d992c-199">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="d992c-200">Permitir que los componentes secundarios registren devoluciones de llamada.</span><span class="sxs-lookup"><span data-stu-id="d992c-200">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="d992c-201">Invoca las devoluciones de llamada registradas durante el evento `OnAfterRender` con la referencia de elemento que se ha pasado.</span><span class="sxs-lookup"><span data-stu-id="d992c-201">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="d992c-202">Este método permite de forma indirecta que los componentes secundarios interactúen con la referencia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="d992c-202">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="d992c-203">En el siguiente ejemplo de WebAssembly de Blazor se ilustra el método.</span><span class="sxs-lookup"><span data-stu-id="d992c-203">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="d992c-204">En la etiqueta `<head>` de *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="d992c-204">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="d992c-205">En la etiqueta `<body>` de *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="d992c-205">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="d992c-206">*Pages/Index.razor* (componente primario):</span><span class="sxs-lookup"><span data-stu-id="d992c-206">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="d992c-207">*Pages/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="d992c-207">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="d992c-208">*Shared/SurveyPrompt.razor* (componente secundario):</span><span class="sxs-lookup"><span data-stu-id="d992c-208">*Shared/SurveyPrompt.razor* (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="d992c-209">*Shared/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="d992c-209">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a><span data-ttu-id="d992c-210">Protección de las llamadas de interoperabilidad de JS</span><span class="sxs-lookup"><span data-stu-id="d992c-210">Harden JS interop calls</span></span>

<span data-ttu-id="d992c-211">La interoperabilidad de JS puede no funcionar debido a errores de red y debe tratarse como no confiable.</span><span class="sxs-lookup"><span data-stu-id="d992c-211">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="d992c-212">De forma predeterminada, una aplicación de servidor Blazor agota el tiempo de espera de las llamadas de interoperabilidad de JS en el servidor transcurrido un minuto.</span><span class="sxs-lookup"><span data-stu-id="d992c-212">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="d992c-213">Si una aplicación puede tolerar un tiempo de espera más restrictivo, como 10 segundos, establézcalo recurriendo a uno de los siguientes métodos:</span><span class="sxs-lookup"><span data-stu-id="d992c-213">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="d992c-214">Globalmente en `Startup.ConfigureServices`, especifique el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="d992c-214">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="d992c-215">Por cada invocación en el código de componente, una sola llamada puede especificar el tiempo de espera:</span><span class="sxs-lookup"><span data-stu-id="d992c-215">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="d992c-216">Para más información sobre el agotamiento de recursos, vea <xref:security/blazor/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="d992c-216">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="d992c-217">Evitar referencias de objetos circulares</span><span class="sxs-lookup"><span data-stu-id="d992c-217">Avoid circular object references</span></span>

<span data-ttu-id="d992c-218">Los objetos que contienen referencias circulares no se pueden serializar en el cliente para:</span><span class="sxs-lookup"><span data-stu-id="d992c-218">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="d992c-219">Llamadas de método .NET.</span><span class="sxs-lookup"><span data-stu-id="d992c-219">.NET method calls.</span></span>
* <span data-ttu-id="d992c-220">Llamadas de método JavaScript desde C# cuando el tipo de valor devuelto tiene referencias circulares.</span><span class="sxs-lookup"><span data-stu-id="d992c-220">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="d992c-221">Para más información, consulte los problemas siguientes:</span><span class="sxs-lookup"><span data-stu-id="d992c-221">For more information, see the following issues:</span></span>

* <span data-ttu-id="d992c-222">[Circular references are not supported, take two )](https://github.com/dotnet/aspnetcore/issues/20525) (No se admiten las referencias circulares, toma dos) (dotnet/aspnetcore #20525</span><span class="sxs-lookup"><span data-stu-id="d992c-222">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="d992c-223">[Proposal: Add mechanism to handle circular references when serializing](https://github.com/dotnet/runtime/issues/30820) (Propuesta: agregar un mecanismo para controlar las referencias circulares al serializar) (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="d992c-223">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d992c-224">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d992c-224">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="d992c-225">Ejemplo de InteropComponent.razor (repositorio de GitHub dotnet/AspNetCore, rama de la versión 3.1)</span><span class="sxs-lookup"><span data-stu-id="d992c-225">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="d992c-226">[Realización de transferencias de gran cantidad de datos en aplicaciones de servidor Blazor](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="d992c-226">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
