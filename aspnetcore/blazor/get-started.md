---
title: Introducción a ASP.NET Core Blazor
author: guardrex
description: Para empezar a usar Blazor, compile una aplicación Blazor con las herramientas que prefiera.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769460"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="b136b-103">Introducción a ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b136b-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="b136b-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b136b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b136b-105">Para empezar a trabajar con Blazor, siga las instrucciones para las herramientas que prefiere:</span><span class="sxs-lookup"><span data-stu-id="b136b-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b136b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b136b-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b136b-107">Para crear aplicaciones de Blazor Server, instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="b136b-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b136b-108">Para crear aplicaciones de Blazor Server y Blazor WebAssembly, instale la versión preliminar más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="b136b-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="b136b-109">Para información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* y *Blazor Server*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b136b-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b136b-110">Instale la plantilla de versión preliminar Blazor WebAssembly mediante la ejecución del comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="b136b-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="b136b-111">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="b136b-111">Create a new project.</span></span>

1. <span data-ttu-id="b136b-112">Seleccione **Aplicación Blazor**.</span><span class="sxs-lookup"><span data-stu-id="b136b-112">Select **Blazor App**.</span></span> <span data-ttu-id="b136b-113">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b136b-113">Select **Next**.</span></span>

1. <span data-ttu-id="b136b-114">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b136b-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b136b-115">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b136b-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b136b-116">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="b136b-116">Select **Create**.</span></span>

1. <span data-ttu-id="b136b-117">Para disfrutar de una experiencia de Blazor WebAssembly (Visual Studio 16.6 Preview 2 o posterior), elija la plantilla **Aplicación de Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="b136b-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b136b-118">Para disfrutar de una experiencia de Blazor Server (Visual Studio 16.4 o posterior), elija la plantilla **Aplicación de Blazor Server**.</span><span class="sxs-lookup"><span data-stu-id="b136b-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b136b-119">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="b136b-119">Select **Create**.</span></span>

1. <span data-ttu-id="b136b-120">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b136b-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b136b-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b136b-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="b136b-122">Instale el [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b136b-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b136b-123">También puede instalar la plantilla de versión preliminar [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) si ejecuta el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="b136b-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="b136b-124">Para información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* y *Blazor Server*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b136b-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b136b-125">El [SDK de .NET Core, versión 3.1.201 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) es **necesario** para usar la plantilla Blazor WebAssembly, versión preliminar 3.2.</span><span class="sxs-lookup"><span data-stu-id="b136b-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="b136b-126">Ejecute `dotnet --version` en un shell de comandos para confirmar la versión instalada del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b136b-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b136b-127">Instale [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="b136b-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="b136b-128">Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="b136b-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="b136b-129">Para disfrutar de una experiencia de Blazor Server, ejecute el comando siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="b136b-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="b136b-130">Para disfrutar de una experiencia de Blazor WebAssembly, ejecute el comando siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="b136b-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="b136b-131">Abra la carpeta *WebApplication1* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b136b-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="b136b-132">El IDE solicita que agregue recursos para compilar y depurar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="b136b-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b136b-133">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="b136b-133">Select **Yes**.</span></span>

1. <span data-ttu-id="b136b-134">Con Blazor Server, ejecute la aplicación con el depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b136b-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="b136b-135">Con Blazor WebAssembly, inicie la aplicación con la configuración de inicio **.NET Core Launch (independiente de Blazor)** e inicie el explorador con la configuración de inicio **Depuración de Blazor Web Assembly para .NET Core en Chrome** (requiere Chrome).</span><span class="sxs-lookup"><span data-stu-id="b136b-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="b136b-136">Para obtener más información, vea <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="b136b-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="b136b-137">En un navegador, vaya a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b136b-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b136b-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b136b-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b136b-139">Blazor Server es compatible con Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="b136b-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="b136b-140">Blazor WebAssembly no es compatible en este momento.</span><span class="sxs-lookup"><span data-stu-id="b136b-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="b136b-141">Para crear aplicaciones Blazor WebAssembly en macOS, siga las instrucciones de la pestaña **CLI de .NET Core**. Para información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* y *Blazor Server*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b136b-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b136b-142">Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b136b-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="b136b-143">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="b136b-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="b136b-144">En la barra lateral, seleccione **.NET Core** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="b136b-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="b136b-145">Seleccione la plantilla de **Aplicación Blazor Server**.</span><span class="sxs-lookup"><span data-stu-id="b136b-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="b136b-146">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b136b-146">Select **Next**.</span></span>

1. <span data-ttu-id="b136b-147">Confirme las configuraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b136b-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="b136b-148">**Plataforma de destino** establecida en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="b136b-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="b136b-149">**Autenticación** establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="b136b-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="b136b-150">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b136b-150">Select **Next**.</span></span>

1. <span data-ttu-id="b136b-151">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="b136b-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="b136b-152">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="b136b-152">Select **Create**.</span></span>

1. <span data-ttu-id="b136b-153">Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*.</span><span class="sxs-lookup"><span data-stu-id="b136b-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="b136b-154">La depuración no se admite en este momento.</span><span class="sxs-lookup"><span data-stu-id="b136b-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="b136b-155">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="b136b-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="b136b-156">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="b136b-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b136b-157">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="b136b-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="b136b-158">Instale el [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="b136b-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="b136b-159">De forma opcional, instale la plantilla de versión preliminar Blazor WebAssembly mediante la ejecución del comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="b136b-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="b136b-160">Para información sobre los dos modelos de hospedaje de Blazor, *Blazor WebAssembly* y *Blazor Server*, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b136b-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="b136b-161">El [SDK de .NET Core, versión 3.1.201 o posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1) es **necesario** para usar la plantilla Blazor WebAssembly, versión preliminar 3.2.</span><span class="sxs-lookup"><span data-stu-id="b136b-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="b136b-162">Ejecute `dotnet --version` en un shell de comandos para confirmar la versión instalada del SDK de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b136b-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="b136b-163">Para disfrutar de una experiencia de Blazor Server, ejecute los comandos siguientes en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="b136b-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="b136b-164">Para disfrutar de una experiencia Blazor WebAssembly, ejecute los comandos siguientes en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="b136b-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="b136b-165">En un navegador, vaya a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b136b-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="b136b-166">Hay varias páginas disponibles en las pestañas de la barra lateral:</span><span class="sxs-lookup"><span data-stu-id="b136b-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="b136b-167">Página principal</span><span class="sxs-lookup"><span data-stu-id="b136b-167">Home</span></span>
* <span data-ttu-id="b136b-168">Contador</span><span class="sxs-lookup"><span data-stu-id="b136b-168">Counter</span></span>
* <span data-ttu-id="b136b-169">Captura de datos</span><span class="sxs-lookup"><span data-stu-id="b136b-169">Fetch data</span></span>

<span data-ttu-id="b136b-170">En la página Contador, seleccione el botón **Click me** para aumentar el contador sin una actualización de página.</span><span class="sxs-lookup"><span data-stu-id="b136b-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="b136b-171">Para incrementar un contador en una página web suele ser necesario escribir JavaScript, pero con Blazor se puede usar C#.</span><span class="sxs-lookup"><span data-stu-id="b136b-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="b136b-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="b136b-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="b136b-173">Una solicitud de `/counter` en el explorador, tal y como se especifica en la directiva de `@page` en la parte superior, hace que el componente `Counter` represente su contenido.</span><span class="sxs-lookup"><span data-stu-id="b136b-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="b136b-174">Los componentes se representan en una representación en memoria del árbol de representación que se puede usar para actualizar la interfaz de usuario de una manera eficaz y flexible.</span><span class="sxs-lookup"><span data-stu-id="b136b-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="b136b-175">Cada vez que se selecciona el botón **Hacer clic aquí**, ocurre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b136b-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="b136b-176">Se desencadena el evento `onclick`.</span><span class="sxs-lookup"><span data-stu-id="b136b-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="b136b-177">Se llama al método `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="b136b-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="b136b-178">El elemento `currentCount` se incrementa.</span><span class="sxs-lookup"><span data-stu-id="b136b-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="b136b-179">El componente se representa de nuevo.</span><span class="sxs-lookup"><span data-stu-id="b136b-179">The component is rendered again.</span></span>

<span data-ttu-id="b136b-180">El tiempo de ejecución compara el contenido nuevo con el anterior y solo aplica el contenido cambiado al Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="b136b-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="b136b-181">Agregue un componente a otro mediante sintaxis HTML.</span><span class="sxs-lookup"><span data-stu-id="b136b-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="b136b-182">Por ejemplo, para agregar el componente `Counter` a la página de inicio de la aplicación, incorpore un elemento `<Counter />` al componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="b136b-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="b136b-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="b136b-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="b136b-184">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b136b-184">Run the app.</span></span> <span data-ttu-id="b136b-185">La página principal tiene su propio contador que proporciona el componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="b136b-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="b136b-186">Los parámetros del componente se especifican mediante atributos o [contenido secundario](xref:blazor/components#child-content), que permiten establecer propiedades en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="b136b-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="b136b-187">Para agregar un parámetro al componente `Counter`, actualice el bloque `@code` del componente:</span><span class="sxs-lookup"><span data-stu-id="b136b-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="b136b-188">Agregue una propiedad pública para `IncrementAmount` con un atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="b136b-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="b136b-189">Cambie el método `IncrementCount` para usar `IncrementAmount` al aumentar el valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b136b-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="b136b-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="b136b-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="b136b-191">Especifique `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo.</span><span class="sxs-lookup"><span data-stu-id="b136b-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="b136b-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="b136b-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="b136b-193">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b136b-193">Run the app.</span></span> <span data-ttu-id="b136b-194">El componente `Index` tiene su propio contador que se incrementa en diez cada vez que se selecciona el botón **Hacer clic aquí**.</span><span class="sxs-lookup"><span data-stu-id="b136b-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="b136b-195">El componente `Counter` (*Counter.razor*) en `/counter` sigue incrementándose en uno.</span><span class="sxs-lookup"><span data-stu-id="b136b-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b136b-196">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="b136b-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="b136b-197">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b136b-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
