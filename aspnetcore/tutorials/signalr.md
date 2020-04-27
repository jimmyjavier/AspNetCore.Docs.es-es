---
title: Introducción a ASP.NET Core SignalR
author: bradygaster
description: En este tutorial, creará una aplicación de chat en la que se usa ASP.NET Core SignalR.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 869eb325ee95a78e4b16c61c5b0573bb094292e3
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994615"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="07645-103">Tutorial: Introducción a SignalR de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="07645-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07645-104">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real con SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="07645-105">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="07645-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="07645-106">Cree un proyecto web.</span><span class="sxs-lookup"><span data-stu-id="07645-106">Create a web project.</span></span>
> * <span data-ttu-id="07645-107">Agregar la biblioteca cliente de SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="07645-108">Crear un concentrador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="07645-109">Configurar el proyecto para usar SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="07645-110">Agregar código que envía mensajes desde cualquier cliente a todos los clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="07645-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="07645-111">Al final, tendrá una aplicación de chat funcional:</span><span class="sxs-lookup"><span data-stu-id="07645-111">At the end, you'll have a working chat app:</span></span>

![Aplicación de ejemplo de SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="07645-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="07645-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07645-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="07645-117">Crear un proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="07645-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07645-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="07645-119">En el menú, seleccione **Archivo > Nuevo proyecto**.</span><span class="sxs-lookup"><span data-stu-id="07645-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="07645-120">En el cuadro de diálogo **Crear un proyecto nuevo**, seleccione **Aplicación web ASP.NET Core** y, a continuación, seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="07645-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="07645-121">En el cuadro de diálogo **Configurar el nuevo proyecto**, asigne al proyecto el nombre *SignalRChat* y, a continuación, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="07645-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="07645-122">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, seleccione **.NET Core** y **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="07645-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="07645-123">Seleccione **Aplicación web** para crear un proyecto en el que se use Razor Pages, y, a continuación, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="07645-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="07645-126">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) en la carpeta en la que vaya a crear la del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="07645-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="07645-127">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="07645-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-128">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07645-129">En el menú, seleccione **Archivo > Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="07645-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="07645-130">Seleccione **.NET Core > Aplicación > Aplicación web** (no **Aplicación web [Modelo-Vista-Controlador]** ) y, a continuación, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="07645-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="07645-131">Asegúrese de que la **Plataforma de destino** esté establecida en **.NET Core 3.0** y, a continuación, seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="07645-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="07645-132">Asigne el nombre *SignalRChat* al proyecto y, después, haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="07645-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="07645-133">Agregar la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-133">Add the SignalR client library</span></span>

<span data-ttu-id="07645-134">La biblioteca de servidor de SignalR se incluye en la plataforma de trabajo compartida de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="07645-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="07645-135">La biblioteca cliente de JavaScript no se incluye automáticamente en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07645-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="07645-136">En este tutorial, usará el Administrador de bibliotecas (LibMan) para obtener la biblioteca cliente de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="07645-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="07645-137">unpkg es una red de entrega de contenido (CDN) que puede entregar todo lo que encuentre en npm, el administrador de paquetes de Node.js.</span><span class="sxs-lookup"><span data-stu-id="07645-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07645-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="07645-139">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Biblioteca del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="07645-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="07645-140">En el cuadro de diálogo **Add Client-Side Library** (Agregar biblioteca del lado cliente), en **Proveedor**, seleccione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="07645-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="07645-141">Para **Biblioteca**, indique `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="07645-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="07645-142">Seleccione **Choose specific files** (Elegir archivos específicos), expanda la carpeta *dist/browser* y seleccione *signalr.js* y *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="07645-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="07645-143">Establezca **Ubicación de destino** en *wwwroot/js/signalr/* y seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="07645-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de la biblioteca](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="07645-145">LibMan crea una carpeta *wwwroot/js/signalr* y copia en ella los archivos seleccionados.</span><span class="sxs-lookup"><span data-stu-id="07645-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="07645-147">En el terminal integrado, ejecute el comando siguiente para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="07645-148">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="07645-149">Puede que deba esperar unos segundos antes de ver la salida.</span><span class="sxs-lookup"><span data-stu-id="07645-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="07645-150">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="07645-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="07645-151">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="07645-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="07645-152">Copie los archivos en el destino *wwwroot/js/signalr*.</span><span class="sxs-lookup"><span data-stu-id="07645-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="07645-153">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="07645-153">Copy only the specified files.</span></span>

  <span data-ttu-id="07645-154">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-155">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07645-156">En el **terminal**, ejecute el siguiente comando para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="07645-157">Vaya a la carpeta de proyecto (la que contiene el archivo *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="07645-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="07645-158">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="07645-159">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="07645-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="07645-160">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="07645-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="07645-161">Copie los archivos en el destino *wwwroot/js/signalr*.</span><span class="sxs-lookup"><span data-stu-id="07645-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="07645-162">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="07645-162">Copy only the specified files.</span></span>

  <span data-ttu-id="07645-163">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="07645-164">Creación de un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-164">Create a SignalR hub</span></span>

<span data-ttu-id="07645-165">Un *concentrador* es una clase que actúa como una canalización general que controla la comunicación entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="07645-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="07645-166">En la carpeta del proyecto SignalRChat, cree una carpeta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="07645-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="07645-167">En la carpeta *Hubs*, cree un archivo *ChatHub.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="07645-168">La clase `ChatHub` hereda de la clase `Hub` de SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="07645-169">La clase `Hub` administra las conexiones, los grupos y la mensajería.</span><span class="sxs-lookup"><span data-stu-id="07645-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="07645-170">Puede llamarse al método `SendMessage` mediante un cliente conectado para enviar un mensaje a todos los clientes.</span><span class="sxs-lookup"><span data-stu-id="07645-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="07645-171">El código de cliente de JavaScript que llama al método se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07645-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="07645-172">El código de SignalR es asincrónico para proporcionar la máxima escalabilidad.</span><span class="sxs-lookup"><span data-stu-id="07645-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="07645-173">Configuración de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-173">Configure SignalR</span></span>

<span data-ttu-id="07645-174">El servidor de SignalR se debe configurar para que pase las solicitudes de SignalR a SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="07645-175">Agregue el código resaltado siguiente al archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="07645-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="07645-176">Estos cambios agregan SignalR al sistema de inserción de dependencias de ASP.NET Core y a los sistemas de enrutamiento.</span><span class="sxs-lookup"><span data-stu-id="07645-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="07645-177">Adición del código de cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-177">Add SignalR client code</span></span>

* <span data-ttu-id="07645-178">Reemplace el contenido de *Pages/Index.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="07645-179">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="07645-179">The preceding code:</span></span>

  * <span data-ttu-id="07645-180">Crea cuadros de texto para el nombre y el mensaje de texto, y un botón de envío.</span><span class="sxs-lookup"><span data-stu-id="07645-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="07645-181">Crea una lista con `id="messagesList"` para mostrar los mensajes que se reciben desde el concentrador SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="07645-182">Incluye las referencias de script en SignalR y el código de aplicación de *chat.js* que se va a crear en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="07645-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="07645-183">En la carpeta *wwwroot/js*, cree un archivo *chat.js* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="07645-184">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="07645-184">The preceding code:</span></span>

  * <span data-ttu-id="07645-185">Crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="07645-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="07645-186">Agrega al botón de envío un controlador que envía mensajes al concentrador.</span><span class="sxs-lookup"><span data-stu-id="07645-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="07645-187">Agrega al objeto de conexión un controlador que recibe mensajes desde el concentrador y los agrega a la lista.</span><span class="sxs-lookup"><span data-stu-id="07645-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="07645-188">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="07645-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="07645-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07645-190">Presione **CTRL+F5** para ejecutar la aplicación sin depurar.</span><span class="sxs-lookup"><span data-stu-id="07645-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07645-192">En el terminal integrado, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-193">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07645-194">En el menú, seleccione **Ejecutar > Iniciar sin depurar**.</span><span class="sxs-lookup"><span data-stu-id="07645-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="07645-195">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="07645-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="07645-196">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar mensaje**.</span><span class="sxs-lookup"><span data-stu-id="07645-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="07645-197">El nombre y el mensaje se muestran en ambas páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="07645-197">The name and message are displayed on both pages instantly.</span></span>

  ![Aplicación de ejemplo de SignalR](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="07645-199">Si la aplicación no funciona, abra las herramientas para desarrolladores del explorador (F12) y vaya a la consola.</span><span class="sxs-lookup"><span data-stu-id="07645-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="07645-200">Es posible que vea errores relacionados con el código HTML y JavaScript.</span><span class="sxs-lookup"><span data-stu-id="07645-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="07645-201">Por ejemplo, suponga que coloca *signalr.js* en una carpeta distinta a la indicada.</span><span class="sxs-lookup"><span data-stu-id="07645-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="07645-202">En ese caso, la referencia a ese archivo no funcionará y verá un error 404 en la consola.</span><span class="sxs-lookup"><span data-stu-id="07645-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="07645-203">![Error: signalr.js no encontrado](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="07645-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="07645-204">Si se produce el error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY en Chrome, ejecute estos comandos para actualizar el certificado de desarrollo:</span><span class="sxs-lookup"><span data-stu-id="07645-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07645-205">En este tutorial se describen los conceptos básicos de la creación de una aplicación en tiempo real con SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="07645-206">Aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="07645-206">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="07645-207">Cree un proyecto web.</span><span class="sxs-lookup"><span data-stu-id="07645-207">Create a web project.</span></span>   
> * <span data-ttu-id="07645-208">Agregar la biblioteca cliente de SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-208">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="07645-209">Crear un concentrador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-209">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="07645-210">Configurar el proyecto para usar SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-210">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="07645-211">Agregar código que envía mensajes desde cualquier cliente a todos los clientes conectados.</span><span class="sxs-lookup"><span data-stu-id="07645-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="07645-212">Al final, tendrá una aplicación de chat funcional: ![Aplicación de ejemplo de SignalR](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="07645-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="07645-213">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="07645-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="07645-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-216">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="07645-217">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="07645-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="07645-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="07645-219">En el menú, seleccione **Archivo > Nuevo proyecto**.</span><span class="sxs-lookup"><span data-stu-id="07645-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="07645-220">En el cuadro de diálogo **Nuevo proyecto**, seleccione **Instalado > Visual C# > Web > Aplicación web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="07645-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="07645-221">Asigne al proyecto el nombre *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="07645-221">Name the project *SignalRChat*.</span></span> 

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="07645-223">Seleccione **Aplicación web** para crear un proyecto en el que se use Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="07645-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="07645-224">Seleccione una plataforma de destino de **.NET Core**, seleccione **ASP.NET Core 2.2** y haga clic en **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="07645-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Cuadro de diálogo Nuevo proyecto en Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="07645-227">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal) en la carpeta en la que vaya a crear la del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="07645-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="07645-228">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="07645-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-229">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="07645-230">En el menú, seleccione **Archivo > Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="07645-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="07645-231">Seleccione **.NET Core > Aplicación > Aplicación web ASP.NET Core** (no seleccione **Aplicación web de ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="07645-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="07645-232">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="07645-232">Select **Next**.</span></span>  

* <span data-ttu-id="07645-233">Asigne el nombre *SignalRChat* al proyecto y, después, haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="07645-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="07645-234">Agregar la biblioteca cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-234">Add the SignalR client library</span></span>   

<span data-ttu-id="07645-235">La biblioteca de servidor de SignalR se incluye en el metapaquete `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="07645-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="07645-236">La biblioteca cliente de JavaScript no se incluye automáticamente en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="07645-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="07645-237">En este tutorial, usará el Administrador de bibliotecas (LibMan) para obtener la biblioteca cliente de *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="07645-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="07645-238">unpkg es una red de entrega de contenido (CDN) que puede entregar todo lo que encuentre en npm, el administrador de paquetes de Node.js.</span><span class="sxs-lookup"><span data-stu-id="07645-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="07645-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="07645-240">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Biblioteca del lado cliente**.</span><span class="sxs-lookup"><span data-stu-id="07645-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="07645-241">En el cuadro de diálogo **Add Client-Side Library** (Agregar biblioteca del lado cliente), en **Proveedor**, seleccione **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="07645-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="07645-242">En **Biblioteca**, escriba `@microsoft/signalr@3` y seleccione la versión más reciente que no sea una versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="07645-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de la biblioteca](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="07645-244">Seleccione **Choose specific files** (Elegir archivos específicos), expanda la carpeta *dist/browser* y seleccione *signalr.js* y *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="07645-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="07645-245">Establezca **Ubicación de destino** en *wwwroot/lib/signalr/* y seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="07645-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Cuadro de diálogo Add Client-Side Library (Agregar biblioteca del lado cliente): selección de archivos y destino](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="07645-247">LibMan crea una carpeta *wwwroot/lib/signalr* y copia en ella los archivos seleccionados.</span><span class="sxs-lookup"><span data-stu-id="07645-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="07645-249">En el terminal integrado, ejecute el comando siguiente para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="07645-250">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="07645-251">Puede que deba esperar unos segundos antes de ver la salida.</span><span class="sxs-lookup"><span data-stu-id="07645-251">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="07645-252">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="07645-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="07645-253">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="07645-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="07645-254">Copie los archivos en el destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="07645-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="07645-255">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="07645-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="07645-256">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-257">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="07645-258">En el **terminal**, ejecute el siguiente comando para instalar LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="07645-259">Vaya a la carpeta de proyecto (la que contiene el archivo *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="07645-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="07645-260">Ejecute el comando siguiente para obtener la biblioteca cliente de SignalR con LibMan.</span><span class="sxs-lookup"><span data-stu-id="07645-260">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="07645-261">Los parámetros especifican las opciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="07645-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="07645-262">Use el proveedor unpkg.</span><span class="sxs-lookup"><span data-stu-id="07645-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="07645-263">Copie los archivos en el destino *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="07645-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="07645-264">Copie solo los archivos especificados.</span><span class="sxs-lookup"><span data-stu-id="07645-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="07645-265">La salida se parece al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="07645-266">Creación de un concentrador de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-266">Create a SignalR hub</span></span> 

<span data-ttu-id="07645-267">Un *concentrador* es una clase que actúa como una canalización general que controla la comunicación entre el cliente y el servidor.</span><span class="sxs-lookup"><span data-stu-id="07645-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="07645-268">En la carpeta del proyecto SignalRChat, cree una carpeta *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="07645-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="07645-269">En la carpeta *Hubs*, cree un archivo *ChatHub.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="07645-270">La clase `ChatHub` hereda de la clase `Hub` de SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="07645-271">La clase `Hub` administra las conexiones, los grupos y la mensajería.</span><span class="sxs-lookup"><span data-stu-id="07645-271">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="07645-272">Puede llamarse al método `SendMessage` mediante un cliente conectado para enviar un mensaje a todos los clientes.</span><span class="sxs-lookup"><span data-stu-id="07645-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="07645-273">El código de cliente de JavaScript que llama al método se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="07645-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="07645-274">El código de SignalR es asincrónico para proporcionar la máxima escalabilidad.</span><span class="sxs-lookup"><span data-stu-id="07645-274">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="07645-275">Configuración de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-275">Configure SignalR</span></span>    

<span data-ttu-id="07645-276">El servidor de SignalR se debe configurar para que pase las solicitudes de SignalR a SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="07645-277">Agregue el código resaltado siguiente al archivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="07645-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="07645-278">Estos cambios agregan SignalR al sistema de inserción de dependencias de ASP.NET Core y a la canalización de software intermedio.</span><span class="sxs-lookup"><span data-stu-id="07645-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="07645-279">Adición del código de cliente de SignalR</span><span class="sxs-lookup"><span data-stu-id="07645-279">Add SignalR client code</span></span>  

* <span data-ttu-id="07645-280">Reemplace el contenido de *Pages/Index.cshtml* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="07645-281">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="07645-281">The preceding code:</span></span>   

  * <span data-ttu-id="07645-282">Crea cuadros de texto para el nombre y el mensaje de texto, y un botón de envío.</span><span class="sxs-lookup"><span data-stu-id="07645-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="07645-283">Crea una lista con `id="messagesList"` para mostrar los mensajes que se reciben desde el concentrador SignalR.</span><span class="sxs-lookup"><span data-stu-id="07645-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="07645-284">Incluye las referencias de script en SignalR y el código de aplicación de *chat.js* que se va a crear en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="07645-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="07645-285">En la carpeta *wwwroot/js*, cree un archivo *chat.js* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="07645-286">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="07645-286">The preceding code:</span></span>   

  * <span data-ttu-id="07645-287">Crea e inicia una conexión.</span><span class="sxs-lookup"><span data-stu-id="07645-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="07645-288">Agrega al botón de envío un controlador que envía mensajes al concentrador.</span><span class="sxs-lookup"><span data-stu-id="07645-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="07645-289">Agrega al objeto de conexión un controlador que recibe mensajes desde el concentrador y los agrega a la lista.</span><span class="sxs-lookup"><span data-stu-id="07645-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="07645-290">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="07645-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="07645-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07645-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="07645-292">Presione **CTRL+F5** para ejecutar la aplicación sin depurar.</span><span class="sxs-lookup"><span data-stu-id="07645-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="07645-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07645-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="07645-294">En el terminal integrado, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="07645-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="07645-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="07645-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07645-296">En el menú, seleccione **Ejecutar > Iniciar sin depurar**.</span><span class="sxs-lookup"><span data-stu-id="07645-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="07645-297">Copie la dirección URL de la barra de direcciones, abra otra instancia o pestaña del explorador, y pegue la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="07645-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="07645-298">Elija cualquier explorador, escriba un nombre y un mensaje, y haga clic en el botón **Enviar mensaje**.</span><span class="sxs-lookup"><span data-stu-id="07645-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="07645-299">El nombre y el mensaje se muestran en ambas páginas al instante.</span><span class="sxs-lookup"><span data-stu-id="07645-299">The name and message are displayed on both pages instantly.</span></span>   

  <span data-ttu-id="07645-300">Aplicación ![SignalR de ejemplo](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="07645-300">![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

> [!TIP]    
> <span data-ttu-id="07645-301">Si la aplicación no funciona, abra las herramientas para desarrolladores del explorador (F12) y vaya a la consola.</span><span class="sxs-lookup"><span data-stu-id="07645-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="07645-302">Es posible que vea errores relacionados con el código HTML y JavaScript.</span><span class="sxs-lookup"><span data-stu-id="07645-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="07645-303">Por ejemplo, suponga que coloca *signalr.js* en una carpeta distinta a la indicada.</span><span class="sxs-lookup"><span data-stu-id="07645-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="07645-304">En ese caso, la referencia a ese archivo no funcionará y verá un error 404 en la consola.</span><span class="sxs-lookup"><span data-stu-id="07645-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="07645-305">![Error: signalr.js no encontrado](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="07645-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="07645-306">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="07645-306">Additional resources</span></span> 
* [<span data-ttu-id="07645-307">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="07645-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
