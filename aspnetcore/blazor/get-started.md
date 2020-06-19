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
ms.openlocfilehash: c90c3bf7ccef420101c66fe04d579920209b066c
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102338"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="eee00-103">Introducción a ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="eee00-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="eee00-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eee00-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="eee00-105">Para empezar a trabajar con Blazor, siga las instrucciones correspondientes a la herramienta de su elección:</span><span class="sxs-lookup"><span data-stu-id="eee00-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eee00-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eee00-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eee00-107">Instale la versión más reciente de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) con la carga de trabajo **Desarrollo de ASP.NET y web**.</span><span class="sxs-lookup"><span data-stu-id="eee00-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="eee00-108">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="eee00-108">Create a new project.</span></span>

1. <span data-ttu-id="eee00-109">Seleccione **Aplicación Blazor** .</span><span class="sxs-lookup"><span data-stu-id="eee00-109">Select **Blazor App**.</span></span> <span data-ttu-id="eee00-110">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="eee00-110">Select **Next**.</span></span>

1. <span data-ttu-id="eee00-111">Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado.</span><span class="sxs-lookup"><span data-stu-id="eee00-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="eee00-112">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="eee00-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="eee00-113">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="eee00-113">Select **Create**.</span></span>

1. <span data-ttu-id="eee00-114">Para disfrutar de una experiencia de WebAssembly de Blazor, elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="eee00-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="eee00-115">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server**.</span><span class="sxs-lookup"><span data-stu-id="eee00-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="eee00-116">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="eee00-116">Select **Create**.</span></span>

   <span data-ttu-id="eee00-117">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="eee00-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="eee00-118">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eee00-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eee00-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eee00-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="eee00-120">Instale la última versión del [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="eee00-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="eee00-121">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="eee00-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="eee00-122">Instale la versión más reciente de [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="eee00-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="eee00-123">Instale la [extensión de C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) más reciente y la extensión del [depurador de JavaScript (nocturno)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) con `debug.javascript.usePreview` establecido en `true`.</span><span class="sxs-lookup"><span data-stu-id="eee00-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="eee00-124">Para establecer `debug.javascript.usePreview` en `true` con la interfaz de usuario de VS Code, abra **Archivo** > **Preferencias** > **Configuración** y busque `debug javascript use preview`.</span><span class="sxs-lookup"><span data-stu-id="eee00-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="eee00-125">Active la casilla para **usar el nuevo depurador de JavaScript en versión preliminar para Node.js y Chrome**.</span><span class="sxs-lookup"><span data-stu-id="eee00-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="eee00-126">Para disfrutar de una experiencia de WebAssembly de Blazor, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="eee00-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="eee00-127">Para disfrutar de una experiencia de Blazor Server, ejecute el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="eee00-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="eee00-128">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="eee00-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="eee00-129">Abra la carpeta *WebApplication1* en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eee00-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="eee00-130">El IDE solicita que agregue recursos para compilar y depurar el proyecto.</span><span class="sxs-lookup"><span data-stu-id="eee00-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="eee00-131">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="eee00-131">Select **Yes**.</span></span>

1. <span data-ttu-id="eee00-132">Presione <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eee00-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eee00-133">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="eee00-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="eee00-134">Instale [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="eee00-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="eee00-135">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="eee00-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="eee00-136">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="eee00-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="eee00-137">Para disfrutar de una experiencia de WebAssembly de Blazor, elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="eee00-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="eee00-138">Para disfrutar de una experiencia de Blazor Server, elija la plantilla **Aplicación Blazor Server**.</span><span class="sxs-lookup"><span data-stu-id="eee00-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="eee00-139">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="eee00-139">Select **Next**.</span></span>

   <span data-ttu-id="eee00-140">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="eee00-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="eee00-141">Confirme las configuraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="eee00-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="eee00-142">**Plataforma de destino** establecida en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="eee00-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="eee00-143">**Autenticación** establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="eee00-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="eee00-144">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="eee00-144">Select **Next**.</span></span>

1. <span data-ttu-id="eee00-145">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="eee00-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="eee00-146">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="eee00-146">Select **Create**.</span></span>

1. <span data-ttu-id="eee00-147">Seleccione **Ejecutar** > **Iniciar sin depurar** para ejecutar la aplicación *sin el depurador*.</span><span class="sxs-lookup"><span data-stu-id="eee00-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="eee00-148">Ejecute la aplicación con **Ejecutar** > **Iniciar depuración** o usando el botón Ejecutar (&#9654;) para ejecutar la aplicación *con el depurador*.</span><span class="sxs-lookup"><span data-stu-id="eee00-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="eee00-149">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="eee00-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="eee00-150">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="eee00-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="eee00-151">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="eee00-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="eee00-152">Instale la última versión del [SDK de .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="eee00-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="eee00-153">Si ya ha instalado el SDK anteriormente, puede averiguar la versión instalada ejecutando el siguiente comando en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="eee00-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="eee00-154">Para disfrutar de una experiencia de WebAssembly de Blazor, ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="eee00-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="eee00-155">Para disfrutar de una experiencia de Blazor Server, ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="eee00-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="eee00-156">Para más información sobre los dos modelos de hospedaje de Blazor (*WebAssembly de Blazor*  y *Blazor Server*), vea <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="eee00-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="eee00-157">En un navegador, vaya a `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eee00-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="eee00-158">Hay varias páginas disponibles en las pestañas de la barra lateral:</span><span class="sxs-lookup"><span data-stu-id="eee00-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="eee00-159">Página principal</span><span class="sxs-lookup"><span data-stu-id="eee00-159">Home</span></span>
* <span data-ttu-id="eee00-160">Contador</span><span class="sxs-lookup"><span data-stu-id="eee00-160">Counter</span></span>
* <span data-ttu-id="eee00-161">Captura de datos</span><span class="sxs-lookup"><span data-stu-id="eee00-161">Fetch data</span></span>

<span data-ttu-id="eee00-162">En la página Contador, seleccione el botón **Click me** para aumentar el contador sin una actualización de página.</span><span class="sxs-lookup"><span data-stu-id="eee00-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="eee00-163">Para incrementar un contador en una página web suele ser necesario escribir JavaScript, pero con Blazor se puede usar C#.</span><span class="sxs-lookup"><span data-stu-id="eee00-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="eee00-164">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="eee00-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="eee00-165">Una solicitud de `/counter` en el explorador, tal y como se especifica en la directiva de `@page` en la parte superior, hace que el componente `Counter` represente su contenido.</span><span class="sxs-lookup"><span data-stu-id="eee00-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="eee00-166">Los componentes se representan en una representación en memoria del árbol de representación que se puede usar para actualizar la interfaz de usuario de una manera eficaz y flexible.</span><span class="sxs-lookup"><span data-stu-id="eee00-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="eee00-167">Cada vez que se selecciona el botón **Hacer clic aquí**, ocurre lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eee00-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="eee00-168">Se desencadena el evento `onclick`.</span><span class="sxs-lookup"><span data-stu-id="eee00-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="eee00-169">Se llama al método `IncrementCount` .</span><span class="sxs-lookup"><span data-stu-id="eee00-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="eee00-170">El elemento `currentCount` se incrementa.</span><span class="sxs-lookup"><span data-stu-id="eee00-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="eee00-171">El componente se representa de nuevo.</span><span class="sxs-lookup"><span data-stu-id="eee00-171">The component is rendered again.</span></span>

<span data-ttu-id="eee00-172">El tiempo de ejecución compara el contenido nuevo con el anterior y solo aplica el contenido cambiado al Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="eee00-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="eee00-173">Agregue un componente a otro mediante sintaxis HTML.</span><span class="sxs-lookup"><span data-stu-id="eee00-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="eee00-174">Por ejemplo, para agregar el componente `Counter` a la página de inicio de la aplicación, incorpore un elemento `<Counter />` al componente `Index`.</span><span class="sxs-lookup"><span data-stu-id="eee00-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="eee00-175">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="eee00-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="eee00-176">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eee00-176">Run the app.</span></span> <span data-ttu-id="eee00-177">La página principal tiene su propio contador que proporciona el componente `Counter`.</span><span class="sxs-lookup"><span data-stu-id="eee00-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="eee00-178">Los parámetros del componente se especifican mediante atributos o [contenido secundario](xref:blazor/components/index#child-content), que permiten establecer propiedades en el componente secundario.</span><span class="sxs-lookup"><span data-stu-id="eee00-178">Component parameters are specified using attributes or [child content](xref:blazor/components/index#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="eee00-179">Para agregar un parámetro al componente `Counter`, actualice el bloque `@code` del componente:</span><span class="sxs-lookup"><span data-stu-id="eee00-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="eee00-180">Agregue una propiedad pública para `IncrementAmount` con un atributo [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="eee00-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="eee00-181">Cambie el método `IncrementCount` para usar `IncrementAmount` al aumentar el valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="eee00-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="eee00-182">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="eee00-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="eee00-183">Especifique `IncrementAmount` en el elemento `<Counter>` del componente `Index` mediante un atributo.</span><span class="sxs-lookup"><span data-stu-id="eee00-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="eee00-184">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="eee00-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="eee00-185">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eee00-185">Run the app.</span></span> <span data-ttu-id="eee00-186">El componente `Index` tiene su propio contador que se incrementa en diez cada vez que se selecciona el botón **Hacer clic aquí**.</span><span class="sxs-lookup"><span data-stu-id="eee00-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="eee00-187">El componente `Counter` (*Counter.razor*) en `/counter` sigue incrementándose en uno.</span><span class="sxs-lookup"><span data-stu-id="eee00-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="eee00-188">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="eee00-188">Next steps</span></span>

<span data-ttu-id="eee00-189">Cree una aplicación Blazor paso a paso:</span><span class="sxs-lookup"><span data-stu-id="eee00-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="eee00-190">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="eee00-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
