---
<span data-ttu-id="5b874-101">title: "Depuración de ASP.NET Core Blazor WebAssembly" author: description: "Aprenda a depurar aplicaciones Blazor".</span><span class="sxs-lookup"><span data-stu-id="5b874-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="5b874-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5b874-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5b874-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5b874-103">'Blazor'</span></span>
- <span data-ttu-id="5b874-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5b874-104">'Identity'</span></span>
- <span data-ttu-id="5b874-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5b874-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5b874-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5b874-106">'Razor'</span></span>
- <span data-ttu-id="5b874-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5b874-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="5b874-108">Depuración de WebAssembly de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b874-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="5b874-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="5b874-109">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="5b874-110">Las aplicaciones de Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="5b874-110">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="5b874-111">También puede depurar la aplicación con Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5b874-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="5b874-112">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="5b874-112">Available scenarios include:</span></span>

* <span data-ttu-id="5b874-113">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="5b874-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="5b874-114">Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="5b874-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="5b874-115">Un solo paso (<kbd>F10</kbd>) por el código.</span><span class="sxs-lookup"><span data-stu-id="5b874-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="5b874-116">Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5b874-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="5b874-117">En la pantalla *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="5b874-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="5b874-118">Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5b874-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="5b874-119">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="5b874-119">For now, you *can't*:</span></span>

* <span data-ttu-id="5b874-120">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="5b874-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="5b874-121">Alcanzar puntos de interrupción durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5b874-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="5b874-122">Seguiremos mejorando la experiencia de depuración en las próximas versiones.</span><span class="sxs-lookup"><span data-stu-id="5b874-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5b874-123">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="5b874-123">Prerequisites</span></span>

<span data-ttu-id="5b874-124">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="5b874-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="5b874-125">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="5b874-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="5b874-126">Google Chrome (versión 70 o posterior)</span><span class="sxs-lookup"><span data-stu-id="5b874-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="5b874-127">Habilitación de la depuración para Visual Studio y Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b874-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="5b874-128">Para habilitar la depuración de una aplicación de Blazor WebAssembly existente, actualice el archivo *launchSettings.json* del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="5b874-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="5b874-129">Una vez que lo actualice, el archivo *launchSettings.json* debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="5b874-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="5b874-130">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="5b874-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="5b874-131">Permite que el IDE detecte que la aplicación es una aplicación de Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="5b874-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="5b874-132">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="5b874-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="5b874-133">Programa para la mejora</span><span class="sxs-lookup"><span data-stu-id="5b874-133">Visual Studio</span></span>

<span data-ttu-id="5b874-134">Para depurar una aplicación de Blazor WebAssembly en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="5b874-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="5b874-135">Cree una nueva aplicación de Blazor WebAssembly hospedada por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b874-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="5b874-136">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="5b874-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="5b874-137">Establezca un punto de interrupción en *Counter.razor* en el método `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="5b874-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="5b874-138">Vaya a la pestaña **Contador** y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="5b874-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Depuración del contador](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="5b874-140">Revise el valor del campo `currentCount` en la ventana de variables locales:</span><span class="sxs-lookup"><span data-stu-id="5b874-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Vista de las variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="5b874-142">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="5b874-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="5b874-143">Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="5b874-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="5b874-144">Establezca un punto de interrupción en la página *FetchData.razor* en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="5b874-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="5b874-145">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="5b874-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="5b874-146">Vaya a la pestaña **Capturar datos** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:</span><span class="sxs-lookup"><span data-stu-id="5b874-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depuración de captura de datos](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="5b874-148">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="5b874-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="5b874-150">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.</span><span class="sxs-lookup"><span data-stu-id="5b874-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="5b874-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b874-151">Visual Studio Code</span></span>

<span data-ttu-id="5b874-152">Para depurar una aplicación de Blazor WebAssembly en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="5b874-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="5b874-153">Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="5b874-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensiones](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador JS en versión preliminar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="5b874-156">Abra una aplicación de Blazor WebAssembly existente con la depuración habilitada.</span><span class="sxs-lookup"><span data-stu-id="5b874-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="5b874-157">Si recibe la notificación siguiente que indica que se requiere una configuración adicional para habilitar la depuración, confirme que tiene instaladas las extensiones correctas y habilitada la depuración de JavaScript en versión preliminar y, luego, recargue la ventana:</span><span class="sxs-lookup"><span data-stu-id="5b874-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Se requiere configuración adicional](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="5b874-159">Una notificación ofrece agregar los recursos necesarios a la aplicación para realizar la compilación y la depuración.</span><span class="sxs-lookup"><span data-stu-id="5b874-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="5b874-160">Seleccione **Sí**:</span><span class="sxs-lookup"><span data-stu-id="5b874-160">Select **Yes**:</span></span>

     ![Agregar los recursos necesarios](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="5b874-162">Iniciar la aplicación en el depurador es un proceso de dos pasos:</span><span class="sxs-lookup"><span data-stu-id="5b874-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="5b874-163">1\.</span><span class="sxs-lookup"><span data-stu-id="5b874-163">1\.</span></span> <span data-ttu-id="5b874-164">**En primer lugar**, inicie la aplicación con la configuración de inicio **Inicio de .NET Core (independiente de Blazor)** .</span><span class="sxs-lookup"><span data-stu-id="5b874-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="5b874-165">2\.</span><span class="sxs-lookup"><span data-stu-id="5b874-165">2\.</span></span> <span data-ttu-id="5b874-166">**Una vez que la aplicación se inicia**, inicie el explorador con la configuración de inicio **Blazor WebAssembly de depuración de .NET Core en Chrome** (requiere Chrome).</span><span class="sxs-lookup"><span data-stu-id="5b874-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="5b874-167">Para usar Edge en lugar de Chrome, cambie el `type` de la configuración de inicio en *.vscode/launch.json* de `pwa-chrome` a `pwa-msedge`.</span><span class="sxs-lookup"><span data-stu-id="5b874-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="5b874-168">Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="5b874-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="5b874-170">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="5b874-170">Debug in the browser</span></span>

1. <span data-ttu-id="5b874-171">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="5b874-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="5b874-172">Presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="5b874-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="5b874-173">El explorador se debe ejecutar con la depuración remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="5b874-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="5b874-174">Si la depuración remota está deshabilitada, se genera una página de error en la que se indica que **no se puede encontrar la pestaña del explorador depurable**.</span><span class="sxs-lookup"><span data-stu-id="5b874-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="5b874-175">La página de error contiene instrucciones para ejecutar el explorador con el puerto de depuración abierto para que el proxy de depuración de Blazor se pueda conectar a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5b874-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="5b874-176">*Cierre todas las instancias del explorador* y reinicie el explorador, tal como se indica.</span><span class="sxs-lookup"><span data-stu-id="5b874-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="5b874-177">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración abre una nueva pestaña del depurador. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5b874-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="5b874-178">Expanda cada ensamblado y busque los archivos de origen *.cs*/ *.razor* disponibles para la depuración.</span><span class="sxs-lookup"><span data-stu-id="5b874-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="5b874-179">Establezca puntos de interrupción, vuelva a la pestaña de la aplicación y los puntos de interrupción se alcanzarán cuando se ejecute el código.</span><span class="sxs-lookup"><span data-stu-id="5b874-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="5b874-180">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="5b874-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="5b874-181"> proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="5b874-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="5b874-182">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="5b874-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="5b874-183">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="5b874-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="5b874-184">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="5b874-184">Browser source maps</span></span>

<span data-ttu-id="5b874-185">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="5b874-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="5b874-186">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="5b874-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="5b874-187">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="5b874-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="5b874-188">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="5b874-188">Troubleshoot</span></span>

<span data-ttu-id="5b874-189">Si se encuentra con errores, la sugerencia siguiente puede ser útil:</span><span class="sxs-lookup"><span data-stu-id="5b874-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="5b874-190">En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="5b874-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="5b874-191">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="5b874-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
