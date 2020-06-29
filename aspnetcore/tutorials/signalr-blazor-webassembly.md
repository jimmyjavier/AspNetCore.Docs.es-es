---
title: Uso de ASP.NET Core SignalR con Blazor WebAssembly
author: guardrex
description: Cree una aplicación de chat que use ASP.NET Core SignalR con Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3f8aeec1e0471bab5034d1dcc8a42023f6b13c0d
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122105"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="d306a-103">Uso de ASP.NET Core SignalR con Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d306a-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="d306a-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d306a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d306a-105">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real usando SignalR con WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="d306a-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="d306a-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="d306a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d306a-107">Crear un proyecto de aplicación hospedado de Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d306a-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="d306a-108">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="d306a-109">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="d306a-110">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d306a-111">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="d306a-111">Add Razor component code for chat</span></span>

<span data-ttu-id="d306a-112">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="d306a-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="d306a-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d306a-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d306a-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="d306a-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d306a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d306a-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d306a-116">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="d306a-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d306a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d306a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d306a-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d306a-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d306a-119">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="d306a-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d306a-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="d306a-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="d306a-121">Creación de un proyecto de aplicación de WebAssembly de Blazor hospedado</span><span class="sxs-lookup"><span data-stu-id="d306a-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="d306a-122">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="d306a-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d306a-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d306a-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="d306a-124">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="d306a-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="d306a-125">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="d306a-125">Create a new project.</span></span>

1. <span data-ttu-id="d306a-126">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d306a-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d306a-127">Escriba `BlazorSignalRApp` en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="d306a-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d306a-128">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="d306a-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d306a-129">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d306a-129">Select **Create**.</span></span>

1. <span data-ttu-id="d306a-130">Elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="d306a-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="d306a-131">En **Avanzado**, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="d306a-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="d306a-132">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d306a-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d306a-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d306a-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d306a-134">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d306a-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="d306a-135">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d306a-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d306a-136">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="d306a-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d306a-137">Visual Studio Code agrega automáticamente la carpeta `.vscode` con los archivos `launch.json` y `tasks.json` generados.</span><span class="sxs-lookup"><span data-stu-id="d306a-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d306a-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d306a-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d306a-139">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="d306a-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d306a-140">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="d306a-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d306a-141">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="d306a-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d306a-142">Elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="d306a-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d306a-143">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d306a-143">Select **Next**.</span></span>

   <span data-ttu-id="d306a-144">Confirme las configuraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="d306a-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="d306a-145">**Plataforma de destino** establecida en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="d306a-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="d306a-146">**Autenticación** establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="d306a-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="d306a-147">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="d306a-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="d306a-148">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="d306a-148">Select **Next**.</span></span>

1. <span data-ttu-id="d306a-149">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="d306a-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="d306a-150">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="d306a-150">Select **Create**.</span></span>

   <span data-ttu-id="d306a-151">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="d306a-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d306a-152">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="d306a-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d306a-153">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="d306a-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d306a-154">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="d306a-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d306a-155">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d306a-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d306a-156">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d306a-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d306a-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d306a-158">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d306a-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d306a-159">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d306a-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d306a-160">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="d306a-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d306a-161">En los resultados de la búsqueda, seleccione el paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="d306a-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="d306a-162">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="d306a-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d306a-163">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="d306a-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d306a-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d306a-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d306a-165">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="d306a-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d306a-166">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d306a-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d306a-167">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto `BlazorSignalRApp.Client` y, después, seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d306a-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d306a-168">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d306a-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d306a-169">Con **Examinar** seleccionado, escriba `Microsoft.AspNetCore.SignalR.Client` en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="d306a-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d306a-170">En los resultados de la búsqueda, active la casilla situada junto al paquete [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="d306a-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="d306a-171">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="d306a-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d306a-172">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="d306a-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d306a-173">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="d306a-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d306a-174">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-174">Add a SignalR hub</span></span>

<span data-ttu-id="d306a-175">En el proyecto `BlazorSignalRApp.Server`, cree una carpeta `Hubs` (plural) y agregue la siguiente clase `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="d306a-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d306a-176">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d306a-177">En el proyecto `BlazorSignalRApp.Server`, abra el archivo `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="d306a-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d306a-178">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="d306a-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="d306a-179">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d306a-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="d306a-180">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d306a-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d306a-181">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="d306a-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d306a-182">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="d306a-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d306a-183">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="d306a-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d306a-184">En el proyecto `BlazorSignalRApp.Client`, abra el archivo `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="d306a-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="d306a-185">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d306a-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="d306a-186">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="d306a-186">Run the app</span></span>

1. <span data-ttu-id="d306a-187">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="d306a-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d306a-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d306a-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d306a-189">En el **Explorador de soluciones**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="d306a-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="d306a-190">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="d306a-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d306a-191">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="d306a-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d306a-192">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="d306a-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d306a-193">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="d306a-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d306a-194">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d306a-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d306a-195">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d306a-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d306a-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d306a-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d306a-197">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor (`.vscode/launch.json`), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="d306a-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="d306a-198">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="d306a-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d306a-199">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="d306a-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d306a-200">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="d306a-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d306a-201">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="d306a-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d306a-202">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d306a-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d306a-203">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d306a-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d306a-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="d306a-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d306a-205">En la barra lateral **Solución**, seleccione el proyecto `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="d306a-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="d306a-206">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="d306a-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d306a-207">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="d306a-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d306a-208">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="d306a-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d306a-209">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="d306a-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d306a-210">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d306a-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d306a-211">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d306a-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d306a-212">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="d306a-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d306a-213">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="d306a-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="d306a-214">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="d306a-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d306a-215">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón para enviar el mensaje.</span><span class="sxs-lookup"><span data-stu-id="d306a-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d306a-216">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="d306a-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="d306a-217">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="d306a-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="d306a-218">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d306a-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="d306a-219">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="d306a-219">Next steps</span></span>

<span data-ttu-id="d306a-220">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="d306a-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d306a-221">Crear un proyecto de aplicación hospedado de Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d306a-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="d306a-222">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="d306a-223">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="d306a-224">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="d306a-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d306a-225">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="d306a-225">Add Razor component code for chat</span></span>

<span data-ttu-id="d306a-226">Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="d306a-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="d306a-227">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d306a-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="d306a-228">[Negociación entre orígenes de SignalR para la autenticación](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="d306a-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
