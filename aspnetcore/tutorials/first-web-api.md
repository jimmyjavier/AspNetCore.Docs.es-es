---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767244"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="7e5fb-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e5fb-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="7e5fb-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7e5fb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7e5fb-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7e5fb-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7e5fb-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-107">Create a web API project.</span></span>
> * <span data-ttu-id="7e5fb-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7e5fb-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="7e5fb-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="7e5fb-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-111">Call the web API with Postman.</span></span>

<span data-ttu-id="7e5fb-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="7e5fb-113">Información general</span><span class="sxs-lookup"><span data-stu-id="7e5fb-113">Overview</span></span>

<span data-ttu-id="7e5fb-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7e5fb-115">API</span><span class="sxs-lookup"><span data-stu-id="7e5fb-115">API</span></span> | <span data-ttu-id="7e5fb-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="7e5fb-116">Description</span></span> | <span data-ttu-id="7e5fb-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="7e5fb-117">Request body</span></span> | <span data-ttu-id="7e5fb-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="7e5fb-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="7e5fb-119">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="7e5fb-119">Get all to-do items</span></span> | <span data-ttu-id="7e5fb-120">Ninguna</span><span class="sxs-lookup"><span data-stu-id="7e5fb-120">None</span></span> | <span data-ttu-id="7e5fb-121">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="7e5fb-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="7e5fb-122">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="7e5fb-122">Get an item by ID</span></span> | <span data-ttu-id="7e5fb-123">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-123">None</span></span> | <span data-ttu-id="7e5fb-124">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="7e5fb-125">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="7e5fb-125">Add a new item</span></span> | <span data-ttu-id="7e5fb-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-126">To-do item</span></span> | <span data-ttu-id="7e5fb-127">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="7e5fb-128">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7e5fb-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7e5fb-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-129">To-do item</span></span> | <span data-ttu-id="7e5fb-130">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-130">None</span></span> |
|<span data-ttu-id="7e5fb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7e5fb-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="7e5fb-132">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7e5fb-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7e5fb-133">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-133">None</span></span> | <span data-ttu-id="7e5fb-134">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-134">None</span></span>|

<span data-ttu-id="7e5fb-135">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-135">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7e5fb-141">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-144">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7e5fb-145">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="7e5fb-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-147">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e5fb-148">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7e5fb-149">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7e5fb-150">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="7e5fb-151">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-151">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e5fb-154">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7e5fb-155">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7e5fb-156">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="7e5fb-157">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="7e5fb-158">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-158">The preceding commands:</span></span>

  * <span data-ttu-id="7e5fb-159">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="7e5fb-160">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7e5fb-162">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-162">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7e5fb-164">Seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7e5fb-166">En el cuadro de diálogo **Configure your new ASP.NET Core Web API** (Configurar la nueva API web de ASP.NET Core), seleccione **Plataforma de destino** de \* *.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="7e5fb-167">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="7e5fb-169">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="7e5fb-170">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="7e5fb-170">Test the API</span></span>

<span data-ttu-id="7e5fb-171">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="7e5fb-172">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e5fb-174">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7e5fb-175">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7e5fb-176">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7e5fb-177">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e5fb-179">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7e5fb-180">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-181">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7e5fb-182">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7e5fb-183">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7e5fb-184">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7e5fb-185">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="7e5fb-186">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-186">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7e5fb-187">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="7e5fb-187">Add a model class</span></span>

<span data-ttu-id="7e5fb-188">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7e5fb-189">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-191">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7e5fb-192">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7e5fb-193">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="7e5fb-194">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7e5fb-195">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7e5fb-196">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e5fb-198">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7e5fb-199">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-200">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7e5fb-201">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-201">Right-click the project.</span></span> <span data-ttu-id="7e5fb-202">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7e5fb-203">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-203">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7e5fb-205">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7e5fb-206">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7e5fb-207">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="7e5fb-208">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7e5fb-209">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7e5fb-210">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-210">Add a database context</span></span>

<span data-ttu-id="7e5fb-211">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7e5fb-212">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="7e5fb-214">Adición de Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7e5fb-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="7e5fb-215">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="7e5fb-216">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="7e5fb-217">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="7e5fb-218">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="7e5fb-219">Siga las instrucciones anteriores para agregar el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="7e5fb-221">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="7e5fb-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="7e5fb-222">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7e5fb-223">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-224">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7e5fb-225">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7e5fb-226">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7e5fb-227">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-227">Register the database context</span></span>

<span data-ttu-id="7e5fb-228">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7e5fb-229">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="7e5fb-230">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="7e5fb-231">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-231">The preceding code:</span></span>

* <span data-ttu-id="7e5fb-232">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7e5fb-233">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7e5fb-234">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="7e5fb-235">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="7e5fb-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-237">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7e5fb-238">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e5fb-239">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="7e5fb-240">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="7e5fb-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="7e5fb-241">Seleccione **TodoItem (TodoApi.Models)** en **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="7e5fb-242">Seleccione **TodoContext (TodoApi.Models)** en **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="7e5fb-243">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-244">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7e5fb-245">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="7e5fb-246">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-246">The preceding commands:</span></span>

* <span data-ttu-id="7e5fb-247">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="7e5fb-248">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="7e5fb-249">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="7e5fb-250">El código generado:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-250">The generated code:</span></span>

* <span data-ttu-id="7e5fb-251">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7e5fb-252">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7e5fb-253">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7e5fb-254">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7e5fb-255">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="7e5fb-256">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="7e5fb-257">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="7e5fb-258">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="7e5fb-259">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="7e5fb-260">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="7e5fb-261">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="7e5fb-262">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="7e5fb-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="7e5fb-263">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7e5fb-264">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7e5fb-265">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="7e5fb-266">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="7e5fb-267">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7e5fb-268">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="7e5fb-269">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="7e5fb-270">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7e5fb-271">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7e5fb-272">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="7e5fb-273">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="7e5fb-273">Install Postman</span></span>

<span data-ttu-id="7e5fb-274">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7e5fb-275">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="7e5fb-276">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-276">Start the web app.</span></span>
* <span data-ttu-id="7e5fb-277">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-277">Start Postman.</span></span>
* <span data-ttu-id="7e5fb-278">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="7e5fb-279">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="7e5fb-280">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="7e5fb-281">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="7e5fb-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="7e5fb-282">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-282">Create a new request.</span></span>
* <span data-ttu-id="7e5fb-283">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7e5fb-284">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="7e5fb-285">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7e5fb-286">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="7e5fb-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7e5fb-287">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7e5fb-288">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-288">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7e5fb-290">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="7e5fb-290">Test the location header URI</span></span>

* <span data-ttu-id="7e5fb-291">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7e5fb-292">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-292">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="7e5fb-294">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-294">Set the method to GET.</span></span>
* <span data-ttu-id="7e5fb-295">Pegue el URI (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="7e5fb-296">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="7e5fb-297">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="7e5fb-297">Examine the GET methods</span></span>

<span data-ttu-id="7e5fb-298">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="7e5fb-299">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="7e5fb-300">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="7e5fb-301">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="7e5fb-302">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="7e5fb-302">Test Get with Postman</span></span>

* <span data-ttu-id="7e5fb-303">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-303">Create a new request.</span></span>
* <span data-ttu-id="7e5fb-304">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="7e5fb-305">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="7e5fb-306">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="7e5fb-307">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7e5fb-308">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-308">Select **Send**.</span></span>

<span data-ttu-id="7e5fb-309">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-309">This app uses an in-memory database.</span></span> <span data-ttu-id="7e5fb-310">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="7e5fb-311">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="7e5fb-312">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="7e5fb-312">Routing and URL paths</span></span>

<span data-ttu-id="7e5fb-313">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7e5fb-314">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7e5fb-315">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="7e5fb-316">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="7e5fb-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7e5fb-317">En este ejemplo, el nombre de clase de controlador es **TodoItems**Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="7e5fb-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="7e5fb-318">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7e5fb-319">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7e5fb-320">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-320">This sample doesn't use a template.</span></span> <span data-ttu-id="7e5fb-321">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7e5fb-322">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7e5fb-323">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7e5fb-324">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-324">Return values</span></span>

<span data-ttu-id="7e5fb-325">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T > type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7e5fb-326">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7e5fb-327">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7e5fb-328">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7e5fb-329">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7e5fb-330">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7e5fb-331">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7e5fb-332">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7e5fb-333">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="7e5fb-334">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-334">The PutTodoItem method</span></span>

<span data-ttu-id="7e5fb-335">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="7e5fb-336">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7e5fb-337">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7e5fb-338">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7e5fb-339">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7e5fb-340">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7e5fb-341">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="7e5fb-342">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="7e5fb-343">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7e5fb-344">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7e5fb-345">Actualice el elemento to-do que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="7e5fb-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7e5fb-346">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-346">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="7e5fb-348">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="7e5fb-349">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7e5fb-350">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7e5fb-351">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7e5fb-352">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7e5fb-353">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="7e5fb-354">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="7e5fb-355">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="7e5fb-355">Prevent over-posting</span></span>

<span data-ttu-id="7e5fb-356">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="7e5fb-357">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="7e5fb-358">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="7e5fb-359">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="7e5fb-360">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="7e5fb-361">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-361">A DTO may be used to:</span></span>

* <span data-ttu-id="7e5fb-362">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-362">Prevent over-posting.</span></span>
* <span data-ttu-id="7e5fb-363">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="7e5fb-364">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="7e5fb-365">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="7e5fb-366">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="7e5fb-367">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="7e5fb-368">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="7e5fb-369">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="7e5fb-370">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="7e5fb-371">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="7e5fb-372">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="7e5fb-373">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e5fb-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="7e5fb-374">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e5fb-375">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7e5fb-376">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-376">Create a web API project.</span></span>
> * <span data-ttu-id="7e5fb-377">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7e5fb-378">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-378">Add a controller.</span></span>
> * <span data-ttu-id="7e5fb-379">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="7e5fb-380">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="7e5fb-381">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-381">Specify return values.</span></span>
> * <span data-ttu-id="7e5fb-382">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="7e5fb-383">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="7e5fb-384">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="7e5fb-385">Información general</span><span class="sxs-lookup"><span data-stu-id="7e5fb-385">Overview</span></span>

<span data-ttu-id="7e5fb-386">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7e5fb-387">API</span><span class="sxs-lookup"><span data-stu-id="7e5fb-387">API</span></span> | <span data-ttu-id="7e5fb-388">Descripción</span><span class="sxs-lookup"><span data-stu-id="7e5fb-388">Description</span></span> | <span data-ttu-id="7e5fb-389">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="7e5fb-389">Request body</span></span> | <span data-ttu-id="7e5fb-390">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="7e5fb-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="7e5fb-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7e5fb-391">GET /api/TodoItems</span></span> | <span data-ttu-id="7e5fb-392">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="7e5fb-392">Get all to-do items</span></span> | <span data-ttu-id="7e5fb-393">Ninguna</span><span class="sxs-lookup"><span data-stu-id="7e5fb-393">None</span></span> | <span data-ttu-id="7e5fb-394">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="7e5fb-394">Array of to-do items</span></span>|
|<span data-ttu-id="7e5fb-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7e5fb-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="7e5fb-396">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="7e5fb-396">Get an item by ID</span></span> | <span data-ttu-id="7e5fb-397">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-397">None</span></span> | <span data-ttu-id="7e5fb-398">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-398">To-do item</span></span>|
|<span data-ttu-id="7e5fb-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7e5fb-399">POST /api/TodoItems</span></span> | <span data-ttu-id="7e5fb-400">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="7e5fb-400">Add a new item</span></span> | <span data-ttu-id="7e5fb-401">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-401">To-do item</span></span> | <span data-ttu-id="7e5fb-402">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-402">To-do item</span></span> |
|<span data-ttu-id="7e5fb-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7e5fb-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="7e5fb-404">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7e5fb-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7e5fb-405">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-405">To-do item</span></span> | <span data-ttu-id="7e5fb-406">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-406">None</span></span> |
|<span data-ttu-id="7e5fb-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7e5fb-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="7e5fb-408">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7e5fb-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7e5fb-409">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-409">None</span></span> | <span data-ttu-id="7e5fb-410">None</span><span class="sxs-lookup"><span data-stu-id="7e5fb-410">None</span></span>|

<span data-ttu-id="7e5fb-411">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-411">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7e5fb-417">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-420">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7e5fb-421">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="7e5fb-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-423">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e5fb-424">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7e5fb-425">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7e5fb-426">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="7e5fb-427">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="7e5fb-428">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-428">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e5fb-431">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7e5fb-432">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7e5fb-433">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="7e5fb-434">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="7e5fb-435">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-436">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7e5fb-437">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-437">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7e5fb-439">Seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7e5fb-441">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, acepte la **plataforma de destino** predeterminada de \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="7e5fb-442">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="7e5fb-444">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="7e5fb-444">Test the API</span></span>

<span data-ttu-id="7e5fb-445">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-445">The project template creates a `values` API.</span></span> <span data-ttu-id="7e5fb-446">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e5fb-448">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7e5fb-449">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7e5fb-450">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7e5fb-451">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e5fb-453">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7e5fb-454">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-455">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7e5fb-456">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7e5fb-457">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7e5fb-458">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7e5fb-459">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="7e5fb-460">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7e5fb-461">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="7e5fb-461">Add a model class</span></span>

<span data-ttu-id="7e5fb-462">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7e5fb-463">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-465">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7e5fb-466">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7e5fb-467">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="7e5fb-468">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7e5fb-469">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7e5fb-470">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e5fb-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e5fb-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e5fb-472">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7e5fb-473">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-474">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7e5fb-475">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-475">Right-click the project.</span></span> <span data-ttu-id="7e5fb-476">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7e5fb-477">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-477">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7e5fb-479">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7e5fb-480">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7e5fb-481">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7e5fb-482">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7e5fb-483">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7e5fb-484">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-484">Add a database context</span></span>

<span data-ttu-id="7e5fb-485">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7e5fb-486">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-488">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7e5fb-489">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-490">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7e5fb-491">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7e5fb-492">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7e5fb-493">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-493">Register the database context</span></span>

<span data-ttu-id="7e5fb-494">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7e5fb-495">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="7e5fb-496">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="7e5fb-497">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-497">The preceding code:</span></span>

* <span data-ttu-id="7e5fb-498">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7e5fb-499">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7e5fb-500">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7e5fb-501">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="7e5fb-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-503">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7e5fb-504">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="7e5fb-505">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="7e5fb-506">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-508">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7e5fb-509">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="7e5fb-510">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="7e5fb-511">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-511">The preceding code:</span></span>

* <span data-ttu-id="7e5fb-512">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="7e5fb-513">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7e5fb-514">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7e5fb-515">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7e5fb-516">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7e5fb-517">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="7e5fb-518">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="7e5fb-519">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="7e5fb-520">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="7e5fb-521">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="7e5fb-522">Incorporación de métodos Get</span><span class="sxs-lookup"><span data-stu-id="7e5fb-522">Add Get methods</span></span>

<span data-ttu-id="7e5fb-523">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="7e5fb-524">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="7e5fb-525">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-525">Stop the app if it's still running.</span></span> <span data-ttu-id="7e5fb-526">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="7e5fb-527">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="7e5fb-528">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="7e5fb-529">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="7e5fb-530">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="7e5fb-530">Routing and URL paths</span></span>

<span data-ttu-id="7e5fb-531">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7e5fb-532">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7e5fb-533">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="7e5fb-534">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="7e5fb-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7e5fb-535">En este ejemplo, el nombre de clase de controlador es **Todo**Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="7e5fb-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="7e5fb-536">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7e5fb-537">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7e5fb-538">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-538">This sample doesn't use a template.</span></span> <span data-ttu-id="7e5fb-539">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7e5fb-540">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7e5fb-541">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7e5fb-542">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="7e5fb-542">Return values</span></span>

<span data-ttu-id="7e5fb-543">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T > type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7e5fb-544">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7e5fb-545">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7e5fb-546">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7e5fb-547">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7e5fb-548">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7e5fb-549">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7e5fb-550">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7e5fb-551">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="7e5fb-552">Prueba del método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="7e5fb-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="7e5fb-553">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7e5fb-554">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="7e5fb-555">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-555">Start the web app.</span></span>
* <span data-ttu-id="7e5fb-556">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-556">Start Postman.</span></span>
* <span data-ttu-id="7e5fb-557">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e5fb-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e5fb-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e5fb-559">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="7e5fb-560">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7e5fb-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7e5fb-561">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="7e5fb-562">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="7e5fb-563">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="7e5fb-564">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-564">Create a new request.</span></span>
  * <span data-ttu-id="7e5fb-565">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="7e5fb-566">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="7e5fb-567">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="7e5fb-568">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7e5fb-569">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-569">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="7e5fb-571">Incorporación de un método Create</span><span class="sxs-lookup"><span data-stu-id="7e5fb-571">Add a Create method</span></span>

<span data-ttu-id="7e5fb-572">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7e5fb-573">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7e5fb-574">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7e5fb-575">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="7e5fb-576">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="7e5fb-577">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7e5fb-578">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="7e5fb-579">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7e5fb-580">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7e5fb-581">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7e5fb-582">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="7e5fb-583">Prueba del método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="7e5fb-584">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-584">Build the project.</span></span>
* <span data-ttu-id="7e5fb-585">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7e5fb-586">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="7e5fb-587">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7e5fb-588">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="7e5fb-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7e5fb-589">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7e5fb-590">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-590">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="7e5fb-592">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7e5fb-593">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="7e5fb-593">Test the location header URI</span></span>

* <span data-ttu-id="7e5fb-594">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7e5fb-595">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-595">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="7e5fb-597">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-597">Set the method to GET.</span></span>
* <span data-ttu-id="7e5fb-598">Pegue el URI (por ejemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="7e5fb-599">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="7e5fb-600">Incorporación de un método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="7e5fb-601">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="7e5fb-602">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7e5fb-603">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7e5fb-604">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7e5fb-605">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7e5fb-606">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7e5fb-607">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="7e5fb-608">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="7e5fb-609">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7e5fb-610">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7e5fb-611">Actualice la tarea pendiente que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="7e5fb-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7e5fb-612">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-612">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="7e5fb-614">Incorporación de un método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="7e5fb-615">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="7e5fb-616">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7e5fb-617">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="7e5fb-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7e5fb-618">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7e5fb-619">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7e5fb-620">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="7e5fb-621">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-621">Select **Send**.</span></span>

<span data-ttu-id="7e5fb-622">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="7e5fb-623">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="7e5fb-624">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e5fb-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="7e5fb-625">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="7e5fb-626">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-626">jQuery initiates the request.</span></span> <span data-ttu-id="7e5fb-627">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="7e5fb-628">Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="7e5fb-629">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="7e5fb-630">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="7e5fb-631">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="7e5fb-632">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="7e5fb-633">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="7e5fb-634">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="7e5fb-635">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="7e5fb-636">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="7e5fb-637">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="7e5fb-638">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="7e5fb-639">Obtención de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="7e5fb-639">Get a list of to-do items</span></span>

<span data-ttu-id="7e5fb-640">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="7e5fb-641">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="7e5fb-642">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="7e5fb-643">Incorporación de una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-643">Add a to-do item</span></span>

<span data-ttu-id="7e5fb-644">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="7e5fb-645">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="7e5fb-646">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="7e5fb-647">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="7e5fb-648">Actualizar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-648">Update a to-do item</span></span>

<span data-ttu-id="7e5fb-649">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="7e5fb-650">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="7e5fb-651">Eliminar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="7e5fb-651">Delete a to-do item</span></span>

<span data-ttu-id="7e5fb-652">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="7e5fb-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="7e5fb-653">Agregar compatibilidad con la autenticación a una API web</span><span class="sxs-lookup"><span data-stu-id="7e5fb-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="7e5fb-654">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7e5fb-654">Additional resources</span></span>

<span data-ttu-id="7e5fb-655">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="7e5fb-656">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e5fb-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7e5fb-657">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="7e5fb-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="7e5fb-658">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="7e5fb-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
