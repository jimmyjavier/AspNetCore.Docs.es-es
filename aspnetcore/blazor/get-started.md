---
title: Introducción a ASP.NET Core Blazor
author: guardrex
description: Para empezar a usar Blazor, compile una aplicación Blazor con las herramientas que prefiera.
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
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243608"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="a275d-103">Introducción a ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a275d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="a275d-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a275d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a275d-105">Para empezar a trabajar con Blazor, siga las instrucciones correspondientes a la herramienta de su elección:</span><span class="sxs-lookup"><span data-stu-id="a275d-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a275d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a275d-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a275d-107">Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="a275d-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="a275d-108">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="a275d-108">Create a new project.</span></span>

1. <span data-ttu-id="a275d-109">Seleccione **Aplicación Blazor** .</span><span class="sxs-lookup"><span data-stu-id="a275d-109">Select **Blazor App**.</span></span> <span data-ttu-id="a275d-110">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="a275d-110">Select **Next**.</span></span>

1. <span data-ttu-id="a275d-111">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="a275d-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a275d-112">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="a275d-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a275d-113">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a275d-113">Select **Create**.</span></span>

1. <span data-ttu-id="a275d-114">Para disfrutar de una experiencia de WebAssembly de Blazor, elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="a275d-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a275d-115">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server**.</span><span class="sxs-lookup"><span data-stu-id="a275d-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a275d-116">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a275d-116">Select **Create**.</span></span>

   <span data-ttu-id="a275d-117">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="a275d-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a275d-118">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a275d-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a275d-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a275d-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a275d-120">Instale la última versión del [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="a275d-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="a275d-121">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a275d-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="a275d-122">Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="a275d-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="a275d-123">Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="a275d-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="a275d-124">Para establecer `debug.javascript.usePreview` en `true` con la interfaz de usuario de VS Code, abra **Archivo** > **Preferencias** > **Configuración** y busque `debug javascript use preview`.</span><span class="sxs-lookup"><span data-stu-id="a275d-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="a275d-125">Active la casilla para **usar el nuevo depurador de JavaScript en versión preliminar para Node.js y Chrome**.</span><span class="sxs-lookup"><span data-stu-id="a275d-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="a275d-126">Para disfrutar de una experiencia de WebAssembly de Blazor, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a275d-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="a275d-127">Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a275d-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="a275d-128">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="a275d-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a275d-129">Abra la carpeta `WebApplication1` en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a275d-129">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="a275d-130">El IDE solicita que agregue recursos para compilar y depurar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="a275d-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a275d-131">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="a275d-131">Select **Yes**.</span></span>

1. <span data-ttu-id="a275d-132">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a275d-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a275d-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a275d-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a275d-134">Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="a275d-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="a275d-135">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="a275d-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a275d-136">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="a275d-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="a275d-137">Para disfrutar de una experiencia de WebAssembly de Blazor, elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="a275d-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a275d-138">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server**.</span><span class="sxs-lookup"><span data-stu-id="a275d-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a275d-139">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="a275d-139">Select **Next**.</span></span>

   <span data-ttu-id="a275d-140">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="a275d-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a275d-141">Confirme las configuraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="a275d-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="a275d-142">**Plataforma de destino** establecida en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="a275d-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="a275d-143">**Autenticación** establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="a275d-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="a275d-144">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="a275d-144">Select **Next**.</span></span>

1. <span data-ttu-id="a275d-145">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="a275d-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a275d-146">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="a275d-146">Select **Create**.</span></span>

1. <span data-ttu-id="a275d-147">Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*.</span><span class="sxs-lookup"><span data-stu-id="a275d-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a275d-148">Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.</span><span class="sxs-lookup"><span data-stu-id="a275d-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="a275d-149">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="a275d-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a275d-150">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="a275d-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a275d-151">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="a275d-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="a275d-152">Instale la última versión del [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="a275d-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="a275d-153">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a275d-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="a275d-154">Para disfrutar de una experiencia de WebAssembly de Blazor, ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a275d-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a275d-155">Para disfrutar de una experiencia de Blazor Server, ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="a275d-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a275d-156">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="a275d-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a275d-157">En un navegador, vaya a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="a275d-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="a275d-158">Hay varias páginas disponibles en las pestañas de la barra lateral:</span><span class="sxs-lookup"><span data-stu-id="a275d-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a275d-159">Página principal</span><span class="sxs-lookup"><span data-stu-id="a275d-159">Home</span></span>
* <span data-ttu-id="a275d-160">Contador</span><span class="sxs-lookup"><span data-stu-id="a275d-160">Counter</span></span>
* <span data-ttu-id="a275d-161">Captura de datos</span><span class="sxs-lookup"><span data-stu-id="a275d-161">Fetch data</span></span>

<span data-ttu-id="a275d-162">En la página Contador, seleccione el botón para aumentar el contador sin una actualización de página.</span><span class="sxs-lookup"><span data-stu-id="a275d-162">On the Counter page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a275d-163">Para incrementar un contador en una página web suele ser necesario escribir JavaScript, pero con Blazor se puede usar C#.</span><span class="sxs-lookup"><span data-stu-id="a275d-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="a275d-164">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="a275d-164">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="a275d-165">Una solicitud de `/counter` en el explorador, tal y como se especifica en la directiva de `@page` en la parte superior, hace que el componente `Counter` represente su contenido.</span><span class="sxs-lookup"><span data-stu-id="a275d-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="a275d-166">Los componentes se representan en una representación en memoria del árbol de representación que se puede usar para actualizar la interfaz de usuario de una manera eficaz y flexible.</span><span class="sxs-lookup"><span data-stu-id="a275d-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a275d-167">Cada vez que se selecciona el botón, ocurre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="a275d-167">Each time the button is selected:</span></span>

* <span data-ttu-id="a275d-168">Se desencadena el evento `onclick`.</span><span class="sxs-lookup"><span data-stu-id="a275d-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a275d-169">Se llama al método `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="a275d-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a275d-170">El elemento `currentCount` se incrementa.</span><span class="sxs-lookup"><span data-stu-id="a275d-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a275d-171">El componente se representa de nuevo.</span><span class="sxs-lookup"><span data-stu-id="a275d-171">The component is rendered again.</span></span>

<span data-ttu-id="a275d-172">El tiempo de ejecución compara el contenido nuevo con el anterior y solo aplica el contenido cambiado al Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="a275d-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a275d-173">Agregue un componente a otro mediante sintaxis HTML.</span><span class="sxs-lookup"><span data-stu-id="a275d-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="a275d-174">Por ejemplo, para agregar el componente `Counter` a la página de inicio de la aplicación, incorpore un elemento `<Counter />` al componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="a275d-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="a275d-175">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="a275d-175">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="a275d-176">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a275d-176">Run the app.</span></span> <span data-ttu-id="a275d-177">La página principal tiene su propio contador que proporciona el componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="a275d-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="a275d-178">Los parámetros del componente se especifican mediante atributos o [contenido secundario](xref:blazor/components/index#child-content), que permiten establecer propiedades en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="a275d-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="a275d-179">Para agregar un parámetro al componente `Counter`, actualice el bloque `@code` del componente:</span><span class="sxs-lookup"><span data-stu-id="a275d-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="a275d-180">Agregue una propiedad pública para `IncrementAmount` con un atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="a275d-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="a275d-181">Cambie el método `IncrementCount` para usar `IncrementAmount` al aumentar el valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="a275d-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a275d-182">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="a275d-182">`Pages/Counter.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="a275d-183">Especifique `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo.</span><span class="sxs-lookup"><span data-stu-id="a275d-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a275d-184">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="a275d-184">`Pages/Index.razor`:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="a275d-185">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a275d-185">Run the app.</span></span> <span data-ttu-id="a275d-186">El componente `Index` tiene su propio contador que se incrementa en diez cada vez que se selecciona el botón.</span><span class="sxs-lookup"><span data-stu-id="a275d-186">The `Index` component has its own counter that increments by ten each time the button is selected.</span></span> <span data-ttu-id="a275d-187">El componente `Counter` (`Pages/Counter.razor`) en `/counter` sigue incrementándose en uno.</span><span class="sxs-lookup"><span data-stu-id="a275d-187">The `Counter` component (`Pages/Counter.razor`) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a275d-188">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="a275d-188">Next steps</span></span>

<span data-ttu-id="a275d-189">Cree una aplicación Blazor paso a paso:</span><span class="sxs-lookup"><span data-stu-id="a275d-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="a275d-190">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a275d-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
