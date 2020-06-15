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
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679597"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="4ee6d-103">Uso de ASP.NET Core SignalR con Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4ee6d-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="4ee6d-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4ee6d-105">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real usando SignalR con WebAssembly de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="4ee6d-106">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ee6d-107">Crear un proyecto de aplicación hospedado de Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4ee6d-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="4ee6d-108">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="4ee6d-109">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="4ee6d-110">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4ee6d-111">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="4ee6d-111">Add Razor component code for chat</span></span>

<span data-ttu-id="4ee6d-112">Al final de este tutorial, tendrá una aplicación de chat funcional.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="4ee6d-113">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ee6d-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4ee6d-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="4ee6d-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ee6d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ee6d-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4ee6d-116">[Versión 16.6 o posterior de Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **Desarrollo web y ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="4ee6d-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ee6d-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ee6d-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ee6d-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ee6d-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="4ee6d-119">Visual Studio para Mac, versión 8.6 o posterior</span><span class="sxs-lookup"><span data-stu-id="4ee6d-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="4ee6d-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4ee6d-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="4ee6d-121">Creación de un proyecto de aplicación de WebAssembly de Blazor hospedado</span><span class="sxs-lookup"><span data-stu-id="4ee6d-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="4ee6d-122">Siga las instrucciones para su elección de herramientas:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ee6d-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ee6d-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="4ee6d-124">Se requiere Visual Studio 16.6 o posterior y el SDK de .NET Core 3.1.300 o posterior.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="4ee6d-125">Cree un nuevo proyecto.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-125">Create a new project.</span></span>

1. <span data-ttu-id="4ee6d-126">Seleccione **Aplicación Blazor** y luego **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="4ee6d-127">Escriba "BlazorSignalRApp" en el campo **Nombre del proyecto**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="4ee6d-128">Confirme que la entrada de **Ubicación** es correcta o proporcione una ubicación para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4ee6d-129">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-129">Select **Create**.</span></span>

1. <span data-ttu-id="4ee6d-130">Elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="4ee6d-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="4ee6d-131">En **Avanzado**, active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="4ee6d-132">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="4ee6d-133">Si ha actualizado o instalado una nueva versión de Visual Studio y la plantilla de WebAssembly de Blazor no aparece en la interfaz de usuario de dicha aplicación, vuelva a instalar la plantilla con el comando `dotnet new` mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ee6d-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ee6d-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4ee6d-135">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="4ee6d-136">En Visual Studio Code, abra la carpeta de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="4ee6d-137">Cuando aparezca el cuadro de diálogo para agregar recursos para compilar y depurar la aplicación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="4ee6d-138">Visual Studio Code agrega automáticamente la carpeta *.vscode* con los archivos *launch.json* y *tasks.json* generados.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ee6d-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ee6d-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4ee6d-140">Instale la versión más reciente de [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) y realice los pasos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="4ee6d-141">Seleccione **Archivo** > **Nueva solución** o cree un proyecto **Nuevo** desde **Ventana de inicio**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="4ee6d-142">En la barra lateral, seleccione **Web y consola** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="4ee6d-143">Elija la plantilla **Aplicación WebAssembly de Blazor** .</span><span class="sxs-lookup"><span data-stu-id="4ee6d-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4ee6d-144">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-144">Select **Next**.</span></span>

   <span data-ttu-id="4ee6d-145">Confirme las configuraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="4ee6d-146">**Plataforma de destino** establecida en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="4ee6d-147">**Autenticación** establecida en **Sin autenticación**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="4ee6d-148">Active la casilla **ASP.NET Core hospedado**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="4ee6d-149">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-149">Select **Next**.</span></span>

1. <span data-ttu-id="4ee6d-150">En el campo **Nombre del proyecto**, asigne un nombre a la aplicación `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="4ee6d-151">Seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-151">Select **Create**.</span></span>

   <span data-ttu-id="4ee6d-152">Si aparece un mensaje para que confíe en el certificado de desarrollo, hágalo y continúe.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="4ee6d-153">Las contraseñas de usuario y de cadena de claves son necesarias para confiar en el certificado.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="4ee6d-154">Abra el proyecto; para ello, vaya a la carpeta del proyecto y abra el archivo de solución del proyecto ( *.sln*).</span><span class="sxs-lookup"><span data-stu-id="4ee6d-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4ee6d-155">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4ee6d-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4ee6d-156">En un shell de comandos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="4ee6d-157">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ee6d-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ee6d-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4ee6d-159">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **BlazorSignalRApp.Client** y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4ee6d-160">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que **Origen del paquete** se ha establecido en *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="4ee6d-161">Con **Examinar** seleccionado, escriba "Microsoft.AspNetCore.SignalR.Client" en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="4ee6d-162">En los resultados de la búsqueda, seleccione el paquete [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) y luego **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="4ee6d-163">Si aparece el cuadro de diálogo **Vista previa de los cambios**, seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4ee6d-164">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ee6d-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ee6d-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4ee6d-166">En **Terminal integrado** (**Ver** > **Terminal** en la barra de herramientas), ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ee6d-167">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ee6d-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4ee6d-168">En la barra lateral **Solución**, haga clic con el botón derecho en el proyecto **BlazorSignalRApp.Client** y seleccione **Administrar paquetes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4ee6d-169">En el cuadro de diálogo **Administrar paquetes NuGet**, confirme que la lista desplegable de origen se ha establecido en *nuget.org*.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="4ee6d-170">Con **Examinar** seleccionado, escriba "Microsoft.AspNetCore.SignalR.Client" en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="4ee6d-171">En los resultados de la búsqueda, active la casilla situada junto al paquete [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) y seleccione **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="4ee6d-172">Si aparece el cuadro de diálogo **Aceptación de la licencia**, seleccione **Acepto** si está de acuerdo con los términos de la licencia.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4ee6d-173">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4ee6d-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4ee6d-174">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="4ee6d-175">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-175">Add a SignalR hub</span></span>

<span data-ttu-id="4ee6d-176">En el proyecto **BlazorSignalRApp.Server**, cree una carpeta de *Hubs* (plural) y agregue la siguiente clase `ChatHub` (*Hubs/ChatHub.cs*):</span><span class="sxs-lookup"><span data-stu-id="4ee6d-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="4ee6d-177">Adición de servicios y de un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="4ee6d-178">En el proyecto **BlazorSignalRApp.Server**, abra el archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="4ee6d-179">Agregue el espacio de nombres para la clase `ChatHub` en la parte superior del archivo:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="4ee6d-180">Agregue servicios de middleware de compresión de respuesta y SignalR a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="4ee6d-181">En `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4ee6d-182">Use el middleware de compresión de respuesta de la parte superior de la configuración de la canalización de procesamiento.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4ee6d-183">Entre los puntos de conexión de los controladores y la reserva del lado cliente, agregue un punto de conexión para el concentrador.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="4ee6d-184">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="4ee6d-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="4ee6d-185">En el proyecto **BlazorSignalRApp.Client**, abra el archivo *Pages/Index.razor*.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="4ee6d-186">Reemplace el marcado con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="4ee6d-187">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="4ee6d-187">Run the app</span></span>

1. <span data-ttu-id="4ee6d-188">Siga las instrucciones para las herramientas:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4ee6d-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ee6d-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4ee6d-190">En **Explorador de soluciones**, seleccione el proyecto **BlazorSignalRApp.Server**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="4ee6d-191">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4ee6d-192">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4ee6d-193">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4ee6d-194">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4ee6d-195">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4ee6d-196">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4ee6d-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4ee6d-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4ee6d-198">Cuando VS Code ofrece crear un perfil de inicio para la aplicación de servidor ( *.vscode/launch.json*), la entrada `program` es similar a la siguiente para apuntar al ensamblado de la aplicación (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="4ee6d-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="4ee6d-199">Presione <kbd>F5</kbd> para ejecutar la aplicación con depuración o <kbd>Ctrl</kbd>+<kbd>F5</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4ee6d-200">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4ee6d-201">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4ee6d-202">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4ee6d-203">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4ee6d-204">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4ee6d-205">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="4ee6d-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4ee6d-206">En la barra lateral **Solución**, seleccione el proyecto **BlazorSignalRApp.Server**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="4ee6d-207">Presione <kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutar la aplicación con depuración o <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> para ejecutarla sin depuración.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4ee6d-208">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4ee6d-209">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4ee6d-210">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4ee6d-211">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4ee6d-212">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4ee6d-213">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="4ee6d-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4ee6d-214">En un shell de comandos, ejecute los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="4ee6d-215">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4ee6d-216">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="4ee6d-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4ee6d-217">El nombre y el mensaje se muestran en ambas páginas al instante:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4ee6d-218">Aplicación de ejemplo de ![SignalR de WebAssembly de Blazor abierta en dos ventanas del explorador que muestran mensajes intercambiados](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4ee6d-219">Citas: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="4ee6d-220">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="4ee6d-220">Next steps</span></span>

<span data-ttu-id="4ee6d-221">En este tutorial ha aprendido a:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ee6d-222">Crear un proyecto de aplicación hospedado de Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="4ee6d-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="4ee6d-223">Adición de la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="4ee6d-224">Agregar un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="4ee6d-225">Agregar servicios de SignalR y un punto de conexión para el concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="4ee6d-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4ee6d-226">Agregar código de componente de Razor para chat</span><span class="sxs-lookup"><span data-stu-id="4ee6d-226">Add Razor component code for chat</span></span>

<span data-ttu-id="4ee6d-227">Para más información sobre la creación de aplicaciones de Blazor, consulte la documentación de Blazor:</span><span class="sxs-lookup"><span data-stu-id="4ee6d-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="4ee6d-228">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4ee6d-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="4ee6d-229">[Negociación entre orígenes de SignalR para la autenticación](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="4ee6d-229">[SignalR cross-origin negotiation for authentication](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span></span>
