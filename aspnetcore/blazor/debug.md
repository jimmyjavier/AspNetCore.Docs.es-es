---
title: Depuración de WebAssembly de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo depurar aplicaciones Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: eaa67d63f6d15249885d78d3de197ae53e73f072
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80381867"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="b4bfb-103">Depuración de WebAssembly de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4bfb-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="b4bfb-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="b4bfb-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b4bfb-105">Las aplicaciones de Blazor WebAssembly se pueden depurar con las herramientas de desarrollo del explorador en exploradores basados en Chromium (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="b4bfb-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="b4bfb-106">También puede depurar la aplicación con Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="b4bfb-107">Entre los escenarios disponibles se incluyen los siguientes:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-107">Available scenarios include:</span></span>

* <span data-ttu-id="b4bfb-108">Establecimiento y eliminación de puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="b4bfb-109">Ejecute la aplicación con compatibilidad de depuración en Visual Studio y Visual Studio Code (compatibilidad con <kbd>F5</kbd>).</span><span class="sxs-lookup"><span data-stu-id="b4bfb-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="b4bfb-110">Un solo paso (<kbd>F10</kbd>) por el código.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="b4bfb-111">Reanude la ejecución de código con <kbd>F8</kbd> en un explorador o con <kbd>F5</kbd> en Visual Studio o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="b4bfb-112">En la pantalla *Variables locales*, observe los valores de las variables locales.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="b4bfb-113">Vea la pila de llamadas, incluidas las cadenas de llamadas que van desde JavaScript hasta .NET y desde .NET a JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="b4bfb-114">Por ahora, *no puede*:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-114">For now, you *can't*:</span></span>

* <span data-ttu-id="b4bfb-115">Inspeccionar las matrices.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-115">Inspect arrays.</span></span>
* <span data-ttu-id="b4bfb-116">Mover el puntero para inspeccionar a los miembros.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-116">Hover to inspect members.</span></span>
* <span data-ttu-id="b4bfb-117">Depurar paso a paso por instrucciones o para salir del código administrado.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="b4bfb-118">Tener compatibilidad total para inspeccionar tipos de valor.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="b4bfb-119">Interrumpir las operaciones ante excepciones no controladas.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="b4bfb-120">Alcanzar puntos de interrupción durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="b4bfb-121">Depurar una aplicación con un trabajo de servicio.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="b4bfb-122">Seguiremos mejorando la experiencia de depuración en las próximas versiones.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b4bfb-123">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="b4bfb-123">Prerequisites</span></span>

<span data-ttu-id="b4bfb-124">La depuración requiere cualquiera de los exploradores siguientes:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="b4bfb-125">Microsoft Edge (versión 80 o posterior)</span><span class="sxs-lookup"><span data-stu-id="b4bfb-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="b4bfb-126">Google Chrome (versión 70 o posterior)</span><span class="sxs-lookup"><span data-stu-id="b4bfb-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="b4bfb-127">Habilitación de la depuración para Visual Studio y Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4bfb-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="b4bfb-128">La depuración está habilitada de manera automática para los proyectos nuevos que se crean con la plantilla de proyecto de Blazor WebAssembly de ASP.NET Core 3.2 Preview 3 o posterior.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="b4bfb-129">Para habilitar la depuración de una aplicación de Blazor WebAssembly existente, actualice el archivo *launchSettings.json* del proyecto de inicio para incluir la propiedad `inspectUri` siguiente en cada perfil de inicio:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="b4bfb-130">Una vez que lo actualice, el archivo *launchSettings.json* debe ser similar a este ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="b4bfb-131">La propiedad `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="b4bfb-132">Permite que el IDE detecte que la aplicación es una aplicación de Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="b4bfb-133">Indica a la infraestructura de depuración de scripts que se conecte al explorador a través del proxy de depuración de Blazor.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="b4bfb-134">Programa para la mejora</span><span class="sxs-lookup"><span data-stu-id="b4bfb-134">Visual Studio</span></span>

<span data-ttu-id="b4bfb-135">Para depurar una aplicación de Blazor WebAssembly en Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="b4bfb-136">Asegúrese de haber [instalado la versión preliminar más reciente de Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 o posterior).</span><span class="sxs-lookup"><span data-stu-id="b4bfb-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="b4bfb-137">Cree una nueva aplicación de Blazor WebAssembly hospedada por ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="b4bfb-138">Presione <kbd>F5</kbd> para ejecutar la aplicación en el depurador.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="b4bfb-139">Establezca un punto de interrupción en *Counter.razor* en el método `IncrementCount`.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="b4bfb-140">Vaya a la pestaña **Contador** y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Depuración del contador](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="b4bfb-142">Revise el valor del campo `currentCount` en la ventana de variables locales:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Vista de las variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="b4bfb-144">Presione <kbd>F5</kbd> para continuar la ejecución.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="b4bfb-145">Al depurar la aplicación de Blazor WebAssembly, también puede depurar el código del servidor:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="b4bfb-146">Establezca un punto de interrupción en la página *FetchData.razor* en `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="b4bfb-147">Establezca un punto de interrupción en `WeatherForecastController` en el método de acción `Get`.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="b4bfb-148">Vaya a la pestaña **Capturar datos** para alcanzar el primer punto de recuperación en el componente `FetchData` justo antes de que emita una solicitud HTTP al servidor:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Depuración de captura de datos](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="b4bfb-150">Presione <kbd>F5</kbd> para continuar la ejecución y, luego, alcance el punto de interrupción en el servidor en `WeatherForecastController`:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Depuración del servidor](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="b4bfb-152">Vuelva a presionar <kbd>F5</kbd> para permitir que la ejecución continúe y vea la tabla de pronóstico meteorológico representada.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="b4bfb-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b4bfb-153">Visual Studio Code</span></span>

<span data-ttu-id="b4bfb-154">Para depurar una aplicación de Blazor WebAssembly en Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="b4bfb-155">Instale la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensiones](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Depurador JS en versión preliminar](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="b4bfb-158">Abra una aplicación de Blazor WebAssembly existente con la depuración habilitada.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="b4bfb-159">Si recibe la notificación siguiente que indica que se requiere una configuración adicional para habilitar la depuración, confirme que tiene instaladas las extensiones correctas y habilitada la depuración de JavaScript en versión preliminar y, luego, recargue la ventana:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Se requiere configuración adicional](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="b4bfb-161">Una notificación ofrece agregar los recursos necesarios a la aplicación para realizar la compilación y la depuración.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="b4bfb-162">Seleccione **Sí**:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-162">Select **Yes**:</span></span>

     ![Agregar los recursos necesarios](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="b4bfb-164">Iniciar la aplicación en el depurador es un proceso de dos pasos:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="b4bfb-165">1\.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-165">1\.</span></span> <span data-ttu-id="b4bfb-166">**En primer lugar**, inicie la aplicación con la configuración de inicio **Inicio de .NET Core (independiente de Blazor)** .</span><span class="sxs-lookup"><span data-stu-id="b4bfb-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="b4bfb-167">2\.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-167">2\.</span></span> <span data-ttu-id="b4bfb-168">**Una vez que la aplicación se inicia**, inicie el explorador con la configuración de inicio **Blazor WebAssembly de depuración de .NET Core en Chrome** (requiere Chrome).</span><span class="sxs-lookup"><span data-stu-id="b4bfb-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="b4bfb-169">Para usar Edge en lugar de Chrome, cambie el `type` de la configuración de inicio en *.vscode/launch.json* de `pwa-chrome` a `pwa-msedge`.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="b4bfb-170">Establezca el punto de interrupción en el método `IncrementCount` en el componente `Counter` y seleccione el botón para alcanzar el punto de interrupción:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Depuración del contenedor en VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="b4bfb-172">Depuración en el explorador</span><span class="sxs-lookup"><span data-stu-id="b4bfb-172">Debug in the browser</span></span>

1. <span data-ttu-id="b4bfb-173">Ejecute una compilación de depuración de la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="b4bfb-174">Presione <kbd>Mayús</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="b4bfb-175">El explorador se debe ejecutar con la depuración remota habilitada.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="b4bfb-176">Si la depuración remota está deshabilitada, se genera una página de error en la que se indica que **no se puede encontrar la pestaña del explorador depurable**.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="b4bfb-177">La página de error contiene instrucciones para ejecutar el explorador con el puerto de depuración abierto para que el proxy de depuración de Blazor se pueda conectar a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="b4bfb-178">*Cierre todas las instancias del explorador* y reinicie el explorador, tal como se indica.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="b4bfb-179">Una vez que el explorador se ejecute con la depuración remota habilitada, el método abreviado de teclado de depuración abre una nueva pestaña del depurador. Tras unos instantes, en la pestaña **Orígenes** se mostrará una lista de los ensamblados .NET en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="b4bfb-180">Expanda cada ensamblado y busque los archivos de origen *.cs*/ *.razor* disponibles para la depuración.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="b4bfb-181">Establezca puntos de interrupción, vuelva a la pestaña de la aplicación y los puntos de interrupción se alcanzarán cuando se ejecute el código.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="b4bfb-182">Una vez que se haya alcanzado un punto de interrupción, examine el código una vez (<kbd>F10</kbd>) o reanude (<kbd>F8</kbd>) la ejecución del código con normalidad.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="b4bfb-183"> proporciona un proxy de depuración que implementa el [protocolo Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) y aumenta el protocolo con información específica de .NET.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="b4bfb-184">Cuando se presiona el método abreviado de teclado de depuración, Blazor apunta a Chrome DevTools en el proxy.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="b4bfb-185">El proxy se conecta a la ventana del explorador que se quiere depurar (de ahí la necesidad de habilitar la depuración remota).</span><span class="sxs-lookup"><span data-stu-id="b4bfb-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="b4bfb-186">Mapas de origen del explorador</span><span class="sxs-lookup"><span data-stu-id="b4bfb-186">Browser source maps</span></span>

<span data-ttu-id="b4bfb-187">Los mapas de origen del explorador permiten al explorador volver a asignar los archivos compilados a sus archivos de código fuente originales y se suelen usar para la depuración del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="b4bfb-188">Sin embargo, en la actualidad Blazor no asigna C# directamente a JavaScript o WASM.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="b4bfb-189">En su lugar, Blazor realiza la interpretación de IL en el explorador, por lo que los mapas de origen no son pertinentes.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="b4bfb-190">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b4bfb-190">Troubleshoot</span></span>

<span data-ttu-id="b4bfb-191">Si se encuentra con errores, la sugerencia siguiente puede ser útil:</span><span class="sxs-lookup"><span data-stu-id="b4bfb-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="b4bfb-192">En la pestaña **Depurador**, abra las herramientas para desarrolladores en el explorador.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="b4bfb-193">En la consola, ejecute `localStorage.clear()` para quitar los puntos de interrupción.</span><span class="sxs-lookup"><span data-stu-id="b4bfb-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
