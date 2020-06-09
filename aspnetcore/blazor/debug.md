---
title: 'title: "Depuración de Blazor WebAssembly con ASP.NET Core" author: guardrex description: "Aprenda a depurar aplicaciones Blazor".'
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239202"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="b99ba-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="b99ba-103">'Blazor'</span></span>

<span data-ttu-id="b99ba-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="b99ba-104">'Identity'</span></span>

<span data-ttu-id="b99ba-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="b99ba-105">'Let's Encrypt'</span></span> <span data-ttu-id="b99ba-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="b99ba-106">'Razor'</span></span>

<span data-ttu-id="b99ba-107">"SignalR" uid: blazor/debug</span><span class="sxs-lookup"><span data-stu-id="b99ba-107">'SignalR' uid: blazor/debug</span></span>

* <span data-ttu-id="b99ba-108">Depuración de WebAssembly de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b99ba-108">Debug ASP.NET Core Blazor WebAssembly</span></span>
* [<span data-ttu-id="b99ba-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b99ba-109">Daniel Roth</span></span>](https://github.com/danroth27)
* <span data-ttu-id="b99ba-110">Las aplicaciones de Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="b99ba-110">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>
* <span data-ttu-id="b99ba-111">También puede depurar la aplicación con Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b99ba-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="b99ba-112">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="b99ba-112">Available scenarios include:</span></span>
* <span data-ttu-id="b99ba-113">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="b99ba-113">Set and remove breakpoints.</span></span>

<span data-ttu-id="b99ba-114">Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b99ba-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>

* <span data-ttu-id="b99ba-115">Un solo paso (<kbd>F10</kbd>) por el código.</span><span class="sxs-lookup"><span data-stu-id="b99ba-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="b99ba-116">Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b99ba-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="b99ba-117">En la pantalla *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="b99ba-117">In the *Locals* display, observe the values of local variables.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b99ba-118">Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b99ba-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="b99ba-119">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="b99ba-119">For now, you *can't*:</span></span>

* <span data-ttu-id="b99ba-120">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="b99ba-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b99ba-121">Alcanzar puntos de interrupción durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b99ba-121">Hit breakpoints during app startup.</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="b99ba-122">Seguiremos mejorando la experiencia de depuración en las próximas versiones.</span><span class="sxs-lookup"><span data-stu-id="b99ba-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

<span data-ttu-id="b99ba-123">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="b99ba-123">Prerequisites</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b99ba-124">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="b99ba-124">Debugging requires either of the following browsers:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b99ba-125">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="b99ba-125">Microsoft Edge (version 80 or later)</span></span>

* <span data-ttu-id="b99ba-126">Google Chrome (versión 70 o posterior)</span><span class="sxs-lookup"><span data-stu-id="b99ba-126">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="b99ba-127">Habilitación de la depuración para Visual Studio y Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b99ba-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="b99ba-128">Para habilitar la depuración de una aplicación de Blazor WebAssembly existente, actualice el archivo *launchSettings.json* del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="b99ba-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

## <a name="visual-studio"></a><span data-ttu-id="b99ba-129">Una vez que lo actualice, el archivo *launchSettings.json* debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b99ba-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

<span data-ttu-id="b99ba-130">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="b99ba-130">The `inspectUri` property:</span></span>

1. <span data-ttu-id="b99ba-131">Permite que el IDE detecte que la aplicación es una aplicación de Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b99ba-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b99ba-132">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b99ba-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>
1. <span data-ttu-id="b99ba-133">El marco de trabajo proporciona los valores de marcador de posición para el protocolo WebSocket (`wsProtocol`), el host (`url.hostname`), el puerto (`url.port`) y el identificador URI del inspector en el explorador iniciado (`browserInspectUri`).</span><span class="sxs-lookup"><span data-stu-id="b99ba-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>
1. <span data-ttu-id="b99ba-134">Programa para la mejora</span><span class="sxs-lookup"><span data-stu-id="b99ba-134">Visual Studio</span></span>

   ![Para depurar una aplicación de Blazor WebAssembly en Visual Studio:](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="b99ba-136">Cree una nueva aplicación de Blazor WebAssembly hospedada por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b99ba-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>

   ![Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="b99ba-138">Establezca un punto de interrupción en *Counter.razor* en el método `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>

<span data-ttu-id="b99ba-139">Vaya a la pestaña **Contador** y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="b99ba-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

1. <span data-ttu-id="b99ba-140">Depuración del contador</span><span class="sxs-lookup"><span data-stu-id="b99ba-140">Debug Counter</span></span>
1. <span data-ttu-id="b99ba-141">Revise el valor del campo `currentCount` en la ventana de variables locales:</span><span class="sxs-lookup"><span data-stu-id="b99ba-141">Check out the value of the `currentCount` field in the locals window:</span></span>
1. <span data-ttu-id="b99ba-142">Vista de las variables locales</span><span class="sxs-lookup"><span data-stu-id="b99ba-142">View locals</span></span>

   ![Presione <kbd>F5</kbd> para continuar la ejecución.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="b99ba-144">Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="b99ba-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

   ![Establezca un punto de interrupción en la página *FetchData.razor* en <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="b99ba-146">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="b99ba-147">Vaya a la pestaña **Capturar datos** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:</span><span class="sxs-lookup"><span data-stu-id="b99ba-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

<span data-ttu-id="b99ba-148">Depuración de captura de datos</span><span class="sxs-lookup"><span data-stu-id="b99ba-148">Debug Fetch Data</span></span>
 
<span data-ttu-id="b99ba-149">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="b99ba-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="b99ba-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b99ba-152">Visual Studio Code</span></span>

1. <span data-ttu-id="b99ba-153">Para depurar una aplicación de Blazor WebAssembly en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="b99ba-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>

   <span data-ttu-id="b99ba-154">Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>
   
   * <span data-ttu-id="b99ba-155">Extensiones</span><span class="sxs-lookup"><span data-stu-id="b99ba-155">Extensions</span></span>
   * <span data-ttu-id="b99ba-156">Depurador JS en versión preliminar</span><span class="sxs-lookup"><span data-stu-id="b99ba-156">JS preview debugger</span></span>
   * <span data-ttu-id="b99ba-157">Depuración de aplicaciones de Blazor WebAssembly independientes</span><span class="sxs-lookup"><span data-stu-id="b99ba-157">Debug standalone Blazor WebAssembly</span></span>

   ![Abra la aplicación de Blazor WebAssembly independiente en VS Code.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="b99ba-159">Si recibe la notificación siguiente que indica que se requiere más información para habilitar la depuración:</span><span class="sxs-lookup"><span data-stu-id="b99ba-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>

1. <span data-ttu-id="b99ba-160">Confirme que tiene instaladas las extensiones correctas.</span><span class="sxs-lookup"><span data-stu-id="b99ba-160">Confirm that you have the correct extensions installed.</span></span>

   ![Confirme que está habilitada la depuración de JavaScript en versión preliminar.](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="b99ba-162">Recargue la ventana.</span><span class="sxs-lookup"><span data-stu-id="b99ba-162">Reload the window.</span></span>

1. <span data-ttu-id="b99ba-163">Se requiere configuración adicional</span><span class="sxs-lookup"><span data-stu-id="b99ba-163">Additional setup required</span></span>

   ![Para iniciar la depuración, use el método abreviado de teclado <kbd>F5</kbd> o el elemento de menú.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="b99ba-165">Cuando se le pida, seleccione la opción **Blazor WebAssembly Debug** (Depuración de Blazor WebAssembly) para iniciar la depuración.</span><span class="sxs-lookup"><span data-stu-id="b99ba-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="b99ba-166">Lista de opciones de depuración disponibles</span><span class="sxs-lookup"><span data-stu-id="b99ba-166">List of available debug options</span></span>

1. <span data-ttu-id="b99ba-167">Se inicia la aplicación independiente y se abre un explorador de depuración.</span><span class="sxs-lookup"><span data-stu-id="b99ba-167">The standalone app is launched, and a debugging browser is opened.</span></span> <span data-ttu-id="b99ba-168">Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="b99ba-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="b99ba-170">Depuración de aplicaciones de Blazor WebAssembly hospedadas</span><span class="sxs-lookup"><span data-stu-id="b99ba-170">Debug hosted Blazor WebAssembly</span></span>

<span data-ttu-id="b99ba-171">Abra la aplicación de Blazor WebAssembly hospedada en VS Code.</span><span class="sxs-lookup"><span data-stu-id="b99ba-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="b99ba-172">Si no hay ninguna configuración de inicio establecida para el proyecto, aparece la notificación siguiente.</span><span class="sxs-lookup"><span data-stu-id="b99ba-172">If there's no launch configuration set for the project, the following notification appears.</span></span>

<span data-ttu-id="b99ba-173">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="b99ba-173">Select **Yes**.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="b99ba-174">Agregar los recursos necesarios</span><span class="sxs-lookup"><span data-stu-id="b99ba-174">Add required assets</span></span> <span data-ttu-id="b99ba-175">En la ventana de selección, seleccione el proyecto *Servidor* dentro de la solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="b99ba-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="b99ba-176">Se genera un archivo *launch.json* con la configuración de inicio para iniciar el depurador.</span><span class="sxs-lookup"><span data-stu-id="b99ba-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

<span data-ttu-id="b99ba-177">Adjuntar a una sesión de depuración existente</span><span class="sxs-lookup"><span data-stu-id="b99ba-177">Attach to an existing debugging session</span></span>

| <span data-ttu-id="b99ba-178">Para adjuntar a una aplicación Blazor en ejecución, cree un archivo *launch.json* con la configuración siguiente:</span><span class="sxs-lookup"><span data-stu-id="b99ba-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>    | <span data-ttu-id="b99ba-179">Solo se permite adjuntar a una sesión de depuración en el caso de las aplicaciones independientes.</span><span class="sxs-lookup"><span data-stu-id="b99ba-179">Attaching to a debugging session is only supported for standalone apps.</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="b99ba-180">Para usar la depuración de pila completa, debe iniciar la aplicación desde VS Code.</span><span class="sxs-lookup"><span data-stu-id="b99ba-180">To use full-stack debugging, you must launch the app from VS Code.</span></span> |
| `url`     | <span data-ttu-id="b99ba-181">Opciones de configuración de inicio</span><span class="sxs-lookup"><span data-stu-id="b99ba-181">Launch configuration options</span></span> <span data-ttu-id="b99ba-182">Estas opciones de configuración de inicio son compatibles con el tipo de depuración `blazorwasm`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span> |
| `browser` | <span data-ttu-id="b99ba-183">Opción</span><span class="sxs-lookup"><span data-stu-id="b99ba-183">Option</span></span> <span data-ttu-id="b99ba-184">Descripción</span><span class="sxs-lookup"><span data-stu-id="b99ba-184">Description</span></span> <span data-ttu-id="b99ba-185">Use `launch` para iniciar y adjuntar una sesión de depuración a una aplicación de Blazor WebAssembly o `attach` para adjuntar una sesión de depuración a una aplicación que ya está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="b99ba-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `trace`   | <span data-ttu-id="b99ba-186">La dirección URL que se va a abrir en el explorador durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="b99ba-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="b99ba-187">Tiene como valor predeterminado `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-187">Defaults to `https://localhost:5001`.</span></span> |
| `hosted`  | <span data-ttu-id="b99ba-188">El explorador que se va a iniciar para la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="b99ba-188">The browser to launch for the debugging session.</span></span> |
| `webRoot` | <span data-ttu-id="b99ba-189">Se establece en `edge` o `chrome`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="b99ba-190">Tiene como valor predeterminado `chrome`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-190">Defaults to `chrome`.</span></span> |
| `timeout` | <span data-ttu-id="b99ba-191">Se usa para generar registros desde el depurador de JS.</span><span class="sxs-lookup"><span data-stu-id="b99ba-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="b99ba-192">Se establece en `true` para generar registros.</span><span class="sxs-lookup"><span data-stu-id="b99ba-192">Set to `true` to generate logs.</span></span> |
| `program` | <span data-ttu-id="b99ba-193">Se debe establecer en `true` si se inicia y depura una aplicación de Blazor WebAssembly hospedada.</span><span class="sxs-lookup"><span data-stu-id="b99ba-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> <span data-ttu-id="b99ba-194">Especifica la ruta de acceso absoluta del servidor web.</span><span class="sxs-lookup"><span data-stu-id="b99ba-194">Specifies the absolute path of the web server.</span></span> |
| `cwd`     | <span data-ttu-id="b99ba-195">Se debe establecer si una aplicación se envía desde una subruta.</span><span class="sxs-lookup"><span data-stu-id="b99ba-195">Should be set if an app is served from a sub-route.</span></span> <span data-ttu-id="b99ba-196">El número de milisegundos que se esperarán para adjuntar la sesión de depuración.</span><span class="sxs-lookup"><span data-stu-id="b99ba-196">The number of milliseconds to wait for the debugging session to attach.</span></span> |
| `env`     | <span data-ttu-id="b99ba-197">El valor predeterminado es 30 000 milisegundos (30 segundos).</span><span class="sxs-lookup"><span data-stu-id="b99ba-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> <span data-ttu-id="b99ba-198">Una referencia al archivo ejecutable para ejecutar el servidor de la aplicación hospedada.</span><span class="sxs-lookup"><span data-stu-id="b99ba-198">A reference to the executable to run the server of the hosted app.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="b99ba-199">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-199">Must be set if `hosted` is `true`.</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="b99ba-200">El directorio de trabajo en el que se va a iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b99ba-200">The working directory to launch the app under.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="b99ba-201">Se debe establecer si `hosted` es `true`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-201">Must be set if `hosted` is `true`.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="b99ba-202">Las variables de entorno que se van a proporcionar al proceso iniciado.</span><span class="sxs-lookup"><span data-stu-id="b99ba-202">The environment variables to provide to the launched process.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="b99ba-203">Solo se aplica si `hosted` está establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="b99ba-203">Only applicable if `hosted` is set to `true`.</span></span>

1. <span data-ttu-id="b99ba-204">Configuraciones de inicio de ejemplo</span><span class="sxs-lookup"><span data-stu-id="b99ba-204">Example launch configurations</span></span>

1. <span data-ttu-id="b99ba-205">Inicie y depure una aplicación de Blazor WebAssembly independiente</span><span class="sxs-lookup"><span data-stu-id="b99ba-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

1. <span data-ttu-id="b99ba-206">Adjuntar a una aplicación en ejecución en una dirección URL especificada</span><span class="sxs-lookup"><span data-stu-id="b99ba-206">Attach to a running app at a specified URL</span></span> <span data-ttu-id="b99ba-207">Inicie y depure una aplicación de Blazor WebAssembly hospedada</span><span class="sxs-lookup"><span data-stu-id="b99ba-207">Launch and debug a hosted Blazor WebAssembly app</span></span> <span data-ttu-id="b99ba-208">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="b99ba-208">Debug in the browser</span></span> <span data-ttu-id="b99ba-209">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b99ba-209">Run a Debug build of the app in the Development environment.</span></span>

<span data-ttu-id="b99ba-210">Presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b99ba-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span> <span data-ttu-id="b99ba-211">El explorador se debe ejecutar con la depuración remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="b99ba-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="b99ba-212">Si la depuración remota está deshabilitada, se genera una página de error en la que se indica que **no se puede encontrar la pestaña del explorador depurable**.</span><span class="sxs-lookup"><span data-stu-id="b99ba-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="b99ba-213">La página de error contiene instrucciones para ejecutar el explorador con el puerto de depuración abierto para que el proxy de depuración de Blazor se pueda conectar a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b99ba-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span>

<span data-ttu-id="b99ba-214">*Cierre todas las instancias del explorador* y reinicie el explorador, tal como se indica.</span><span class="sxs-lookup"><span data-stu-id="b99ba-214">*Close all browser instances* and restart the browser as instructed.</span></span> <span data-ttu-id="b99ba-215">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración abre una nueva pestaña del depurador. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b99ba-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="b99ba-216">Expanda cada ensamblado y busque los archivos de origen *.cs*/ *.razor* disponibles para la depuración.</span><span class="sxs-lookup"><span data-stu-id="b99ba-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b99ba-217">Establezca puntos de interrupción, vuelva a la pestaña de la aplicación y los puntos de interrupción se alcanzarán cuando se ejecute el código.</span><span class="sxs-lookup"><span data-stu-id="b99ba-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span>

<span data-ttu-id="b99ba-218">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="b99ba-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span> Blazor<span data-ttu-id="b99ba-219"> proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="b99ba-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b99ba-220">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="b99ba-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b99ba-221">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="b99ba-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

<span data-ttu-id="b99ba-222">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="b99ba-222">Browser source maps</span></span>

* <span data-ttu-id="b99ba-223">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="b99ba-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b99ba-224">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="b99ba-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span>
* <span data-ttu-id="b99ba-225">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="b99ba-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span> <span data-ttu-id="b99ba-226">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b99ba-226">Troubleshoot</span></span>
