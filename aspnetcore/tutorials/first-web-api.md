---
title: 'Tutorial: Creación de una API web con ASP.NET Core'
author: rick-anderson
description: Aprenda a crear de una API web con ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417658"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="e77c9-103">Tutorial: Creación de una API web con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e77c9-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="e77c9-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) y [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="e77c9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="e77c9-105">En este tutorial se enseñan los conceptos básicos de la compilación de una API web con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e77c9-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e77c9-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="e77c9-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e77c9-107">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-107">Create a web API project.</span></span>
> * <span data-ttu-id="e77c9-108">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e77c9-109">Aplicar scaffolding a un controlador con métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="e77c9-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="e77c9-110">Configurar el enrutamiento, las rutas de acceso URL y los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="e77c9-111">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="e77c9-111">Call the web API with Postman.</span></span>

<span data-ttu-id="e77c9-112">Al final, tendrá una API web que pueda administrar los elementos "to-do" almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="e77c9-113">Información general</span><span class="sxs-lookup"><span data-stu-id="e77c9-113">Overview</span></span>

<span data-ttu-id="e77c9-114">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="e77c9-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e77c9-115">API</span><span class="sxs-lookup"><span data-stu-id="e77c9-115">API</span></span> | <span data-ttu-id="e77c9-116">Descripción</span><span class="sxs-lookup"><span data-stu-id="e77c9-116">Description</span></span> | <span data-ttu-id="e77c9-117">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="e77c9-117">Request body</span></span> | <span data-ttu-id="e77c9-118">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="e77c9-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e77c9-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e77c9-119">GET /api/TodoItems</span></span> | <span data-ttu-id="e77c9-120">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="e77c9-120">Get all to-do items</span></span> | <span data-ttu-id="e77c9-121">Ninguna</span><span class="sxs-lookup"><span data-stu-id="e77c9-121">None</span></span> | <span data-ttu-id="e77c9-122">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="e77c9-122">Array of to-do items</span></span>|
|<span data-ttu-id="e77c9-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e77c9-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e77c9-124">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="e77c9-124">Get an item by ID</span></span> | <span data-ttu-id="e77c9-125">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-125">None</span></span> | <span data-ttu-id="e77c9-126">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-126">To-do item</span></span>|
|<span data-ttu-id="e77c9-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e77c9-127">POST /api/TodoItems</span></span> | <span data-ttu-id="e77c9-128">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="e77c9-128">Add a new item</span></span> | <span data-ttu-id="e77c9-129">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-129">To-do item</span></span> | <span data-ttu-id="e77c9-130">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-130">To-do item</span></span> |
|<span data-ttu-id="e77c9-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e77c9-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e77c9-132">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e77c9-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e77c9-133">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-133">To-do item</span></span> | <span data-ttu-id="e77c9-134">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-134">None</span></span> |
|<span data-ttu-id="e77c9-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e77c9-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e77c9-136">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e77c9-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e77c9-137">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-137">None</span></span> | <span data-ttu-id="e77c9-138">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-138">None</span></span>|

<span data-ttu-id="e77c9-139">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-139">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e77c9-145">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e77c9-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e77c9-149">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="e77c9-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-151">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e77c9-152">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e77c9-153">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e77c9-154">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.1** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="e77c9-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="e77c9-155">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-155">Select the **API** template and click **Create**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e77c9-158">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e77c9-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e77c9-159">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e77c9-160">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="e77c9-161">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="e77c9-162">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="e77c9-162">The preceding commands:</span></span>

  * <span data-ttu-id="e77c9-163">Crean un nuevo proyecto de API web y lo abren en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e77c9-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="e77c9-164">Agregan los paquetes de NuGet que se requieren en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e77c9-166">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-166">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e77c9-168">Seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="e77c9-170">En el cuadro de diálogo **Configure your new ASP.NET Core Web API** (Configurar la nueva API web de ASP.NET Core), seleccione **Plataforma de destino** de \* *.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="e77c9-171">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="e77c9-173">Abra un terminal de comandos en la carpeta del proyecto y ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="e77c9-174">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="e77c9-174">Test the API</span></span>

<span data-ttu-id="e77c9-175">La plantilla del proyecto crea una API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="e77c9-176">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e77c9-178">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e77c9-179">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/WeatherForecast`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e77c9-180">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e77c9-181">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e77c9-183">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e77c9-184">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e77c9-186">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e77c9-187">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e77c9-188">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="e77c9-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e77c9-189">Anexe `/WeatherForecast` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="e77c9-190">Se devuelve un JSON similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="e77c9-191">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="e77c9-191">Add a model class</span></span>

<span data-ttu-id="e77c9-192">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="e77c9-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e77c9-193">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="e77c9-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-195">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e77c9-196">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e77c9-197">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="e77c9-198">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e77c9-199">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e77c9-200">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e77c9-202">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e77c9-203">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e77c9-205">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-205">Right-click the project.</span></span> <span data-ttu-id="e77c9-206">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e77c9-207">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-207">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e77c9-209">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e77c9-210">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e77c9-211">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="e77c9-212">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="e77c9-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e77c9-213">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e77c9-214">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="e77c9-214">Add a database context</span></span>

<span data-ttu-id="e77c9-215">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e77c9-216">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="e77c9-218">Adición de Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e77c9-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="e77c9-219">En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="e77c9-220">Seleccione la pestaña **Examinar** y escriba **Microsoft.EntityFrameworkCore.SqlServer** en el cuadro de búsqueda.</span><span class="sxs-lookup"><span data-stu-id="e77c9-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="e77c9-221">Seleccione **Microsoft.EntityFrameworkCore.SqlServer** en el panel izquierdo.</span><span class="sxs-lookup"><span data-stu-id="e77c9-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="e77c9-222">Active la casilla **Proyecto** en el panel derecho y, después, seleccione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="e77c9-223">Siga las instrucciones anteriores para agregar el paquete NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Administrador de paquetes de NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="e77c9-225">Adición del contexto de la base de datos TodoContext</span><span class="sxs-lookup"><span data-stu-id="e77c9-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="e77c9-226">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e77c9-227">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-228">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e77c9-229">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e77c9-230">Escriba el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="e77c9-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e77c9-231">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="e77c9-231">Register the database context</span></span>

<span data-ttu-id="e77c9-232">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e77c9-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e77c9-233">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="e77c9-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="e77c9-234">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="e77c9-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="e77c9-235">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e77c9-235">The preceding code:</span></span>

* <span data-ttu-id="e77c9-236">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="e77c9-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e77c9-237">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="e77c9-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e77c9-238">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="e77c9-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="e77c9-239">Scaffolding de un controlador</span><span class="sxs-lookup"><span data-stu-id="e77c9-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-241">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e77c9-242">Seleccione **Agregar** > **Nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e77c9-243">Seleccione **Controlador de API con acciones mediante Entity Framework** y, después, seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="e77c9-244">En el cuadro de diálogo **Add API Controller with actions, using Entity Framework** (Agregar controlador de API con acciones mediante Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="e77c9-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="e77c9-245">Seleccione **TodoItem (TodoApi.Models)** en **Clase de modelo**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="e77c9-246">Seleccione **TodoContext (TodoApi.Models)** en **Clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="e77c9-247">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-248">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e77c9-249">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="e77c9-250">Los comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="e77c9-250">The preceding commands:</span></span>

* <span data-ttu-id="e77c9-251">Agregan los paquetes NuGet necesarios para realizar scaffolding.</span><span class="sxs-lookup"><span data-stu-id="e77c9-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="e77c9-252">Instalan el motor de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="e77c9-253">Aplican scaffolding a `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="e77c9-254">El código generado:</span><span class="sxs-lookup"><span data-stu-id="e77c9-254">The generated code:</span></span>

* <span data-ttu-id="e77c9-255">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="e77c9-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e77c9-256">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e77c9-257">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="e77c9-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e77c9-258">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e77c9-259">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="e77c9-260">Las plantillas de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="e77c9-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="e77c9-261">Controladores con vistas incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="e77c9-262">Controladores de API no incluyen `[action]` en la plantilla de ruta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="e77c9-263">Si el token `[action]` no está en la plantilla de ruta, el nombre de la [acción](xref:mvc/controllers/routing#action) se excluye de la ruta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="e77c9-264">Es decir, el nombre del método asociado a la acción no se usa en la ruta coincidente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="e77c9-265">Examen del método create de PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="e77c9-266">Reemplace la instrucción return en `PostTodoItem` para usar el operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="e77c9-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="e77c9-267">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="e77c9-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e77c9-268">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e77c9-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e77c9-269">El método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="e77c9-270">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="e77c9-271">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="e77c9-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e77c9-272">Agrega un encabezado [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="e77c9-273">El encabezado `Location` especifica el [URI](https://developer.mozilla.org/docs/Glossary/URI) de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="e77c9-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="e77c9-274">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e77c9-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e77c9-275">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e77c9-276">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="e77c9-277">Instalación de Postman</span><span class="sxs-lookup"><span data-stu-id="e77c9-277">Install Postman</span></span>

<span data-ttu-id="e77c9-278">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e77c9-279">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="e77c9-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="e77c9-280">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-280">Start the web app.</span></span>
* <span data-ttu-id="e77c9-281">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="e77c9-281">Start Postman.</span></span>
* <span data-ttu-id="e77c9-282">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="e77c9-283">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="e77c9-284">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="e77c9-285">Prueba de PostTodoItem con Postman</span><span class="sxs-lookup"><span data-stu-id="e77c9-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="e77c9-286">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="e77c9-286">Create a new request.</span></span>
* <span data-ttu-id="e77c9-287">Establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e77c9-288">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="e77c9-289">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="e77c9-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e77c9-290">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="e77c9-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e77c9-291">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e77c9-292">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-292">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e77c9-294">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="e77c9-294">Test the location header URI</span></span>

* <span data-ttu-id="e77c9-295">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e77c9-296">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="e77c9-296">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="e77c9-298">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="e77c9-298">Set the method to GET.</span></span>
* <span data-ttu-id="e77c9-299">Pegue el URI (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e77c9-300">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="e77c9-301">Examen de los métodos GET</span><span class="sxs-lookup"><span data-stu-id="e77c9-301">Examine the GET methods</span></span>

<span data-ttu-id="e77c9-302">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="e77c9-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="e77c9-303">Llame a los dos puntos de conexión desde un explorador o Postman para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="e77c9-304">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e77c9-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="e77c9-305">La llamada a `GetTodoItems` genera una respuesta similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="e77c9-306">Prueba de Get con Postman</span><span class="sxs-lookup"><span data-stu-id="e77c9-306">Test Get with Postman</span></span>

* <span data-ttu-id="e77c9-307">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="e77c9-307">Create a new request.</span></span>
* <span data-ttu-id="e77c9-308">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="e77c9-309">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="e77c9-310">Por ejemplo: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="e77c9-311">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="e77c9-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e77c9-312">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-312">Select **Send**.</span></span>

<span data-ttu-id="e77c9-313">Esta aplicación utiliza una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="e77c9-313">This app uses an in-memory database.</span></span> <span data-ttu-id="e77c9-314">Si la aplicación se detiene y se inicia, la solicitud GET precedente no devolverá ningún dato.</span><span class="sxs-lookup"><span data-stu-id="e77c9-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="e77c9-315">Si no se devuelve ningún dato, publique los datos en la aplicación con [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="e77c9-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="e77c9-316">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="e77c9-316">Routing and URL paths</span></span>

<span data-ttu-id="e77c9-317">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e77c9-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e77c9-318">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="e77c9-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e77c9-319">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="e77c9-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="e77c9-320">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="e77c9-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e77c9-321">En este ejemplo, el nombre de clase de controlador es **TodoItems**Controller; por tanto, el nombre del controlador es "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="e77c9-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="e77c9-322">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e77c9-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e77c9-323">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="e77c9-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e77c9-324">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="e77c9-324">This sample doesn't use a template.</span></span> <span data-ttu-id="e77c9-325">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e77c9-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e77c9-326">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e77c9-327">Al invocar a `GetTodoItem`, el valor `"{id}"` de la URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e77c9-328">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="e77c9-328">Return values</span></span>

<span data-ttu-id="e77c9-329">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T > type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e77c9-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e77c9-330">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e77c9-331">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="e77c9-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e77c9-332">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="e77c9-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e77c9-333">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="e77c9-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e77c9-334">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e77c9-335">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="e77c9-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e77c9-336">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="e77c9-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e77c9-337">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e77c9-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="e77c9-338">Método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-338">The PutTodoItem method</span></span>

<span data-ttu-id="e77c9-339">Examine el método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e77c9-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="e77c9-340">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e77c9-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e77c9-341">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e77c9-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e77c9-342">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="e77c9-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e77c9-343">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e77c9-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e77c9-344">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e77c9-345">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="e77c9-346">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e77c9-347">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e77c9-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e77c9-348">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e77c9-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e77c9-349">Actualice el elemento to-do que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="e77c9-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e77c9-350">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="e77c9-350">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="e77c9-352">Método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="e77c9-353">Examine el método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e77c9-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e77c9-354">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e77c9-355">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e77c9-356">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e77c9-357">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="e77c9-358">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="e77c9-359">Prevención del exceso de publicación</span><span class="sxs-lookup"><span data-stu-id="e77c9-359">Prevent over-posting</span></span>

<span data-ttu-id="e77c9-360">Actualmente, la aplicación de ejemplo expone todo el objeto `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="e77c9-361">Las aplicaciones de producción suelen limitar los datos que se escriben y se devuelven mediante un subconjunto del modelo.</span><span class="sxs-lookup"><span data-stu-id="e77c9-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="e77c9-362">Hay varias razones para ello y la seguridad es una de las principales.</span><span class="sxs-lookup"><span data-stu-id="e77c9-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="e77c9-363">El subconjunto de un modelo se suele conocer como un objeto de transferencia de datos (DTO), modelo de entrada o modelo de vista.</span><span class="sxs-lookup"><span data-stu-id="e77c9-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="e77c9-364">En este artículo, se usa **DTO**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="e77c9-365">Se puede usar un DTO para:</span><span class="sxs-lookup"><span data-stu-id="e77c9-365">A DTO may be used to:</span></span>

* <span data-ttu-id="e77c9-366">Evitar el exceso de publicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-366">Prevent over-posting.</span></span>
* <span data-ttu-id="e77c9-367">Ocultar las propiedades que los clientes no deben ver.</span><span class="sxs-lookup"><span data-stu-id="e77c9-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="e77c9-368">Omitir algunas propiedades para reducir el tamaño de la carga.</span><span class="sxs-lookup"><span data-stu-id="e77c9-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="e77c9-369">Acoplar los gráficos de objetos que contienen objetos anidados.</span><span class="sxs-lookup"><span data-stu-id="e77c9-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="e77c9-370">Los gráficos de objetos acoplados pueden ser más cómodos para los clientes.</span><span class="sxs-lookup"><span data-stu-id="e77c9-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="e77c9-371">Para mostrar el enfoque del DTO, actualice la clase `TodoItem` a fin de que incluya un campo secreto:</span><span class="sxs-lookup"><span data-stu-id="e77c9-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="e77c9-372">El campo secreto debe ocultarse en esta aplicación, pero una aplicación administrativa podría decidir exponerlo.</span><span class="sxs-lookup"><span data-stu-id="e77c9-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="e77c9-373">Compruebe que puede publicar y obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="e77c9-374">Cree un modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="e77c9-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="e77c9-375">Actualice el valor de `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="e77c9-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="e77c9-376">Compruebe que no puede publicar ni obtener el campo secreto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e77c9-377">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="e77c9-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="e77c9-378">Consulte [Tutorial: Llamada a una API web de ASP.NET Core con JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="e77c9-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e77c9-379">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="e77c9-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e77c9-380">Crear un proyecto de API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-380">Create a web API project.</span></span>
> * <span data-ttu-id="e77c9-381">Agregar una clase de modelo y un contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="e77c9-382">Agregar un controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-382">Add a controller.</span></span>
> * <span data-ttu-id="e77c9-383">Agregar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="e77c9-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="e77c9-384">Configurar el enrutamiento y las rutas de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="e77c9-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="e77c9-385">Especificar los valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-385">Specify return values.</span></span>
> * <span data-ttu-id="e77c9-386">Llamar a la API web con Postman.</span><span class="sxs-lookup"><span data-stu-id="e77c9-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="e77c9-387">Llamar a la API web con JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e77c9-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="e77c9-388">Al final, tendrá una API web que pueda administrar las tareas "pendientes" almacenadas en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="e77c9-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="e77c9-389">Información general</span><span class="sxs-lookup"><span data-stu-id="e77c9-389">Overview</span></span>

<span data-ttu-id="e77c9-390">En este tutorial se crea la siguiente API:</span><span class="sxs-lookup"><span data-stu-id="e77c9-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="e77c9-391">API</span><span class="sxs-lookup"><span data-stu-id="e77c9-391">API</span></span> | <span data-ttu-id="e77c9-392">Descripción</span><span class="sxs-lookup"><span data-stu-id="e77c9-392">Description</span></span> | <span data-ttu-id="e77c9-393">Cuerpo de la solicitud</span><span class="sxs-lookup"><span data-stu-id="e77c9-393">Request body</span></span> | <span data-ttu-id="e77c9-394">Cuerpo de la respuesta</span><span class="sxs-lookup"><span data-stu-id="e77c9-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="e77c9-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e77c9-395">GET /api/TodoItems</span></span> | <span data-ttu-id="e77c9-396">Obtener todas las tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="e77c9-396">Get all to-do items</span></span> | <span data-ttu-id="e77c9-397">Ninguna</span><span class="sxs-lookup"><span data-stu-id="e77c9-397">None</span></span> | <span data-ttu-id="e77c9-398">Matriz de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="e77c9-398">Array of to-do items</span></span>|
|<span data-ttu-id="e77c9-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e77c9-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="e77c9-400">Obtener un elemento por identificador</span><span class="sxs-lookup"><span data-stu-id="e77c9-400">Get an item by ID</span></span> | <span data-ttu-id="e77c9-401">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-401">None</span></span> | <span data-ttu-id="e77c9-402">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-402">To-do item</span></span>|
|<span data-ttu-id="e77c9-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="e77c9-403">POST /api/TodoItems</span></span> | <span data-ttu-id="e77c9-404">Incorporación de un nuevo elemento</span><span class="sxs-lookup"><span data-stu-id="e77c9-404">Add a new item</span></span> | <span data-ttu-id="e77c9-405">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-405">To-do item</span></span> | <span data-ttu-id="e77c9-406">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-406">To-do item</span></span> |
|<span data-ttu-id="e77c9-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="e77c9-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="e77c9-408">Actualizar un elemento existente &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e77c9-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="e77c9-409">Tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-409">To-do item</span></span> | <span data-ttu-id="e77c9-410">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-410">None</span></span> |
|<span data-ttu-id="e77c9-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e77c9-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="e77c9-412">Eliminar un elemento &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="e77c9-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="e77c9-413">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-413">None</span></span> | <span data-ttu-id="e77c9-414">None</span><span class="sxs-lookup"><span data-stu-id="e77c9-414">None</span></span>|

<span data-ttu-id="e77c9-415">En el diagrama siguiente, se muestra el diseño de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-415">The following diagram shows the design of the app.</span></span>

![El cliente está representado por un cuadro a la izquierda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="e77c9-421">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e77c9-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-424">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="e77c9-425">Creación de un proyecto web</span><span class="sxs-lookup"><span data-stu-id="e77c9-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-427">En el menú **Archivo**, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e77c9-428">Seleccione la plantilla **Aplicación web ASP.NET Core** y haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="e77c9-429">Asigne al proyecto el nombre *TodoApi* y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="e77c9-430">En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 2.2** estén seleccionadas.</span><span class="sxs-lookup"><span data-stu-id="e77c9-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="e77c9-431">Seleccione la plantilla **API** y haga clic en **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="e77c9-432">**No** seleccione **Habilitar compatibilidad con Docker**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-432">**Don't** select **Enable Docker Support**.</span></span>

![Cuadro de diálogo de nuevo proyecto de VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e77c9-435">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e77c9-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="e77c9-436">Cambie los directorios (`cd`) a la carpeta que va a contener la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="e77c9-437">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="e77c9-438">Estos comandos crean un nuevo proyecto de API web y abren una nueva instancia de Visual Studio Code en la carpeta del proyecto nuevo.</span><span class="sxs-lookup"><span data-stu-id="e77c9-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="e77c9-439">Cuando en un cuadro de diálogo se le pregunte si quiere agregar al proyecto los recursos necesarios, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-440">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e77c9-441">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-441">Select **File** > **New Solution**.</span></span>

  ![macOS: Nueva solución](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="e77c9-443">Seleccione **.NET Core** > **Aplicación** > **API** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="e77c9-445">En el cuadro de diálogo **Configurar la nueva API web de ASP.NET Core**, acepte la **plataforma de destino** predeterminada de \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="e77c9-446">Escriba *TodoApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![cuadro de diálogo de configuración](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="e77c9-448">Prueba de la API</span><span class="sxs-lookup"><span data-stu-id="e77c9-448">Test the API</span></span>

<span data-ttu-id="e77c9-449">La plantilla del proyecto crea una API `values`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-449">The project template creates a `values` API.</span></span> <span data-ttu-id="e77c9-450">Llame al método `Get` desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e77c9-452">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e77c9-453">Visual Studio inicia un explorador y navega hasta `https://localhost:<port>/api/values`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="e77c9-454">Si aparece un cuadro de diálogo en que se le pregunta si debe confiar en el certificado de IIS Express, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="e77c9-455">En el cuadro de diálogo **Advertencia de seguridad** que aparece a continuación, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e77c9-457">Presione Ctrl+F5 para ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="e77c9-458">En un explorador, vaya a la siguiente dirección URL: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-459">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e77c9-460">Seleccione **Ejecutar** > **Iniciar depuración** para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="e77c9-461">Visual Studio para Mac inicia un explorador y navega hasta `https://localhost:<port>`, donde `<port>` es un número de puerto elegido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="e77c9-462">Se devuelve un error HTTP 404 (No encontrado).</span><span class="sxs-lookup"><span data-stu-id="e77c9-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="e77c9-463">Anexe `/api/values` a la dirección URL (cambie la dirección URL a `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="e77c9-464">Se devuelve el siguiente JSON:</span><span class="sxs-lookup"><span data-stu-id="e77c9-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="e77c9-465">Incorporación de una clase de modelo</span><span class="sxs-lookup"><span data-stu-id="e77c9-465">Add a model class</span></span>

<span data-ttu-id="e77c9-466">Un *modelo* es un conjunto de clases que representan los datos que la aplicación administra.</span><span class="sxs-lookup"><span data-stu-id="e77c9-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="e77c9-467">El modelo para esta aplicación es una clase `TodoItem` única.</span><span class="sxs-lookup"><span data-stu-id="e77c9-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-469">En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="e77c9-470">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e77c9-471">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="e77c9-472">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e77c9-473">Asigne a la clase el nombre *TodoItem* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="e77c9-474">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e77c9-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e77c9-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e77c9-476">Agregue una carpeta denominada *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="e77c9-477">Agregue una clase `TodoItem` a la carpeta *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-478">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e77c9-479">Haga clic con el botón derecho en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-479">Right-click the project.</span></span> <span data-ttu-id="e77c9-480">Seleccione **Agregar** > **Nueva carpeta**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="e77c9-481">Asigne a la carpeta el nombre *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-481">Name the folder *Models*.</span></span>

  ![nueva carpeta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="e77c9-483">Haga clic con el botón derecho en la carpeta *Modelos* y seleccione **Agregar** > **Nuevo archivo** > **General** > **Clase vacía**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="e77c9-484">Asigne a la clase el nombre *TodoItem* y haga clic en **Nuevo**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="e77c9-485">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="e77c9-486">La propiedad `Id` funciona como clave única en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="e77c9-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="e77c9-487">Las clases de modelo pueden ir en cualquier lugar del proyecto, pero convencionalmente e usa la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="e77c9-488">Incorporación de un contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="e77c9-488">Add a database context</span></span>

<span data-ttu-id="e77c9-489">El *contexto de base de datos* es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="e77c9-490">Esta clase se crea derivándola de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-492">Haga clic con el botón derecho en la carpeta *Models* y seleccione **Agregar** > **Clase**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="e77c9-493">Asigne a la clase el nombre *TodoContext* y haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-494">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e77c9-495">Agregue una clase `TodoContext` a la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="e77c9-496">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="e77c9-497">Registro del contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="e77c9-497">Register the database context</span></span>

<span data-ttu-id="e77c9-498">En ASP.NET Core, los servicios (como el contexto de la base de datos) deben registrarse con el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e77c9-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e77c9-499">El contenedor proporciona el servicio a los controladores.</span><span class="sxs-lookup"><span data-stu-id="e77c9-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="e77c9-500">Actualice *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="e77c9-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="e77c9-501">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e77c9-501">The preceding code:</span></span>

* <span data-ttu-id="e77c9-502">Elimina las declaraciones `using` no utilizadas.</span><span class="sxs-lookup"><span data-stu-id="e77c9-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="e77c9-503">Agrega el contexto de base de datos para el contenedor de DI.</span><span class="sxs-lookup"><span data-stu-id="e77c9-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="e77c9-504">Especifica que el contexto de base de datos usará una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="e77c9-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="e77c9-505">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="e77c9-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-507">Haga clic con el botón derecho en la carpeta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="e77c9-508">Seleccione **Agregar** > **Nuevo elemento**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="e77c9-509">En el cuadro de diálogo **Agregar nuevo elemento**, seleccione la plantilla **Clase de controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="e77c9-510">Asigne a la clase el nombre *TodoController* y seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Cuadro de diálogo Agregar nuevo elemento con la palabra "controller" en el cuadro de búsqueda y la opción Clase de controlador de API web seleccionada](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-512">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e77c9-513">En la carpeta *Controllers*, cree una clase denominada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="e77c9-514">Reemplace el código de plantilla por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="e77c9-515">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e77c9-515">The preceding code:</span></span>

* <span data-ttu-id="e77c9-516">Define una clase de controlador de API sin métodos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="e77c9-517">Marca la clase con el atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="e77c9-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="e77c9-518">Este atributo indica que el controlador responde a las solicitudes de la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="e77c9-519">Para información sobre comportamientos específicos que permite el atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="e77c9-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="e77c9-520">Utiliza la inserción de dependencias para insertar el contexto de base de datos (`TodoContext`) en el controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="e77c9-521">El contexto de base de datos se usa en cada uno de los métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) del controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="e77c9-522">Si la base de datos está vacía, le agrega un elemento denominado `Item1`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="e77c9-523">Este código está en el constructor, de manera que se ejecuta cada vez que hay una nueva solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e77c9-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="e77c9-524">Si elimina todos los elementos, el constructor volverá a crear `Item1` la próxima vez que se llame a un método de API.</span><span class="sxs-lookup"><span data-stu-id="e77c9-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="e77c9-525">De este modo, es posible que parezca que la eliminación no ha funcionado, cuando en realidad sí lo ha hecho.</span><span class="sxs-lookup"><span data-stu-id="e77c9-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="e77c9-526">Incorporación de métodos Get</span><span class="sxs-lookup"><span data-stu-id="e77c9-526">Add Get methods</span></span>

<span data-ttu-id="e77c9-527">Para proporcionar una API que recupere tareas pendientes, agregue estos métodos a la clase `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="e77c9-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="e77c9-528">Estos métodos implementan dos puntos de conexión GET:</span><span class="sxs-lookup"><span data-stu-id="e77c9-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="e77c9-529">Detenga la aplicación si todavía está en ejecución.</span><span class="sxs-lookup"><span data-stu-id="e77c9-529">Stop the app if it's still running.</span></span> <span data-ttu-id="e77c9-530">Luego, ejecútela de nuevo para incluir los últimos cambios.</span><span class="sxs-lookup"><span data-stu-id="e77c9-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="e77c9-531">Llame a los dos puntos de conexión desde un explorador para probar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="e77c9-532">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e77c9-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="e77c9-533">La llamada a `GetTodoItems` genera la siguiente respuesta HTTP:</span><span class="sxs-lookup"><span data-stu-id="e77c9-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="e77c9-534">Enrutamiento y rutas URL</span><span class="sxs-lookup"><span data-stu-id="e77c9-534">Routing and URL paths</span></span>

<span data-ttu-id="e77c9-535">El atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica un método que responde a una solicitud HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="e77c9-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="e77c9-536">La ruta de dirección URL para cada método se construye como sigue:</span><span class="sxs-lookup"><span data-stu-id="e77c9-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="e77c9-537">Comience por la cadena de plantilla en el atributo `Route` del controlador:</span><span class="sxs-lookup"><span data-stu-id="e77c9-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="e77c9-538">Reemplace `[controller]` por el nombre del controlador, que convencionalmente es el nombre de clase de controlador sin el sufijo "Controller".</span><span class="sxs-lookup"><span data-stu-id="e77c9-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="e77c9-539">En este ejemplo, el nombre de clase de controlador es **Todo**Controller; por tanto, el nombre del controlador es "todo".</span><span class="sxs-lookup"><span data-stu-id="e77c9-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="e77c9-540">El [enrutamiento](xref:mvc/controllers/routing) en ASP.NET Core no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="e77c9-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="e77c9-541">Si el atributo `[HttpGet]` tiene una plantilla de ruta (por ejemplo, `[HttpGet("products")]`), anéxela a la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="e77c9-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="e77c9-542">En este ejemplo no se usa una plantilla.</span><span class="sxs-lookup"><span data-stu-id="e77c9-542">This sample doesn't use a template.</span></span> <span data-ttu-id="e77c9-543">Para más información, vea [Enrutamiento mediante atributos con atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="e77c9-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="e77c9-544">En el siguiente método `GetTodoItem`, `"{id}"` es una variable de marcador de posición correspondiente al identificador único de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="e77c9-545">Al invocar a `GetTodoItem`, el valor `"{id}"` de la dirección URL se proporciona al método en su parámetro `id`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="e77c9-546">Valores devueltos</span><span class="sxs-lookup"><span data-stu-id="e77c9-546">Return values</span></span>

<span data-ttu-id="e77c9-547">El tipo de valor devuelto de los métodos `GetTodoItems` y `GetTodoItem` es [ActionResult\<T > type](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="e77c9-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="e77c9-548">ASP.NET Core serializa automáticamente el objeto a [JSON](https://www.json.org/) y escribe el JSON en el cuerpo del mensaje de respuesta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="e77c9-549">El código de respuesta para este tipo de valor devuelto es el 200, suponiendo que no haya ninguna excepción no controlada.</span><span class="sxs-lookup"><span data-stu-id="e77c9-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="e77c9-550">Las excepciones no controladas se convierten en errores 5xx.</span><span class="sxs-lookup"><span data-stu-id="e77c9-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="e77c9-551">Los tipos de valores devueltos `ActionResult` pueden representar una gama amplia de códigos de estado HTTP.</span><span class="sxs-lookup"><span data-stu-id="e77c9-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="e77c9-552">Por ejemplo, `GetTodoItem` puede devolver dos valores de estado diferentes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="e77c9-553">Si no hay ningún elemento que coincida con el identificador solicitado, el método devolverá un código de error 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="e77c9-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="e77c9-554">En caso contrario, el método devuelve 200 con un cuerpo de respuesta JSON.</span><span class="sxs-lookup"><span data-stu-id="e77c9-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="e77c9-555">Devolver `item` genera una respuesta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="e77c9-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="e77c9-556">Prueba del método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="e77c9-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="e77c9-557">En este tutorial se usa Postman para probar la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="e77c9-558">Instale [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="e77c9-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="e77c9-559">Inicie la aplicación web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-559">Start the web app.</span></span>
* <span data-ttu-id="e77c9-560">Inicie Postman.</span><span class="sxs-lookup"><span data-stu-id="e77c9-560">Start Postman.</span></span>
* <span data-ttu-id="e77c9-561">Deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e77c9-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e77c9-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e77c9-563">En **Archivo** > **Configuración** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e77c9-564">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e77c9-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="e77c9-565">En **Postman** > **Preferencias** (pestaña **General**), deshabilite **Comprobación del certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="e77c9-566">También puede seleccionar la llave inglesa, hacer clic en **Configuración** y deshabilitar la comprobación del certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="e77c9-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="e77c9-567">Vuelva a habilitar la comprobación del certificado SSL tras probar el controlador.</span><span class="sxs-lookup"><span data-stu-id="e77c9-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="e77c9-568">Cree una nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="e77c9-568">Create a new request.</span></span>
  * <span data-ttu-id="e77c9-569">Establezca el método HTTP en **GET**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="e77c9-570">Establezca la dirección URL de la solicitud en `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="e77c9-571">Por ejemplo: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="e77c9-572">Establezca **Vista de dos paneles** en Postman.</span><span class="sxs-lookup"><span data-stu-id="e77c9-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="e77c9-573">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-573">Select **Send**.</span></span>

![Postman con solicitud Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="e77c9-575">Incorporación de un método Create</span><span class="sxs-lookup"><span data-stu-id="e77c9-575">Add a Create method</span></span>

<span data-ttu-id="e77c9-576">Agregue el siguiente método `PostTodoItem` en *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="e77c9-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="e77c9-577">El código anterior es un método HTTP POST, indicado por el atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="e77c9-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="e77c9-578">El método obtiene el valor de tareas pendientes del cuerpo de la solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="e77c9-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="e77c9-579">El método `CreatedAtAction` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="e77c9-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="e77c9-580">Devuelve un código de estado HTTP 201 cuando se ha ejecutado correctamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="e77c9-581">HTTP 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="e77c9-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="e77c9-582">Agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e77c9-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="e77c9-583">El encabezado `Location` especifica el identificador URI de la tarea pendiente recién creada.</span><span class="sxs-lookup"><span data-stu-id="e77c9-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="e77c9-584">Para obtener más información, consulte [10.2.2 201 creado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="e77c9-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="e77c9-585">Hace referencia a la acción `GetTodoItem` para crear el identificador URI del encabezado `Location`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="e77c9-586">La palabra clave `nameof` de C# se usa para evitar que se codifique de forma rígida el nombre de acción en la llamada a `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="e77c9-587">Prueba del método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="e77c9-588">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-588">Build the project.</span></span>
* <span data-ttu-id="e77c9-589">En Postman, establezca el método HTTP en `POST`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="e77c9-590">Seleccione la pestaña **Cuerpo**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="e77c9-591">Seleccione el botón de radio **Raw** (Sin formato).</span><span class="sxs-lookup"><span data-stu-id="e77c9-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="e77c9-592">Establezca el tipo en **JSON (application/json)** .</span><span class="sxs-lookup"><span data-stu-id="e77c9-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="e77c9-593">En el cuerpo de la solicitud, introduzca JSON para una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="e77c9-594">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-594">Select **Send**.</span></span>

  ![Postman con solicitud de creación](first-web-api/_static/create.png)

  <span data-ttu-id="e77c9-596">Si recibe un error 405 (Método no permitido), probablemente sea el resultado de no haber compilado el proyecto después de agregar el método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="e77c9-597">Prueba del URI del encabezado de ubicación</span><span class="sxs-lookup"><span data-stu-id="e77c9-597">Test the location header URI</span></span>

* <span data-ttu-id="e77c9-598">Seleccione la pestaña **Encabezados** en el panel **Respuesta**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="e77c9-599">Copie el valor de encabezado **Ubicación**:</span><span class="sxs-lookup"><span data-stu-id="e77c9-599">Copy the **Location** header value:</span></span>

  ![Pestaña Encabezados de la consola de Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="e77c9-601">Establezca el método en GET.</span><span class="sxs-lookup"><span data-stu-id="e77c9-601">Set the method to GET.</span></span>
* <span data-ttu-id="e77c9-602">Pegue el URI (por ejemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="e77c9-603">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="e77c9-604">Incorporación de un método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="e77c9-605">Agregue el siguiente método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e77c9-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="e77c9-606">`PutTodoItem` es similar a `PostTodoItem`, salvo por el hecho de que usa HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="e77c9-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="e77c9-607">La respuesta es [204 Sin contenido](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e77c9-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="e77c9-608">Según la especificación HTTP, una solicitud PUT requiere que el cliente envíe toda la entidad actualizada, no solo los cambios.</span><span class="sxs-lookup"><span data-stu-id="e77c9-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="e77c9-609">Para admitir actualizaciones parciales, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="e77c9-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="e77c9-610">Si recibe un error al llamar a `PutTodoItem`, llame a `GET` para asegurarse de que hay un elemento en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="e77c9-611">Prueba del método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="e77c9-612">En este ejemplo se usa una base de datos en memoria que se debe inicializar cada vez que se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e77c9-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="e77c9-613">Debe haber un elemento en la base de datos antes de que realice una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e77c9-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="e77c9-614">Llame a GET para asegurarse de que hay un elemento en la base de datos antes de realizar una llamada PUT.</span><span class="sxs-lookup"><span data-stu-id="e77c9-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="e77c9-615">Actualice la tarea pendiente que tiene el id. = 1 y establezca su nombre en "feed fish":</span><span class="sxs-lookup"><span data-stu-id="e77c9-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="e77c9-616">En la imagen siguiente, se muestra la actualización de Postman:</span><span class="sxs-lookup"><span data-stu-id="e77c9-616">The following image shows the Postman update:</span></span>

![Consola de Postman que muestra la respuesta 204 (Sin contenido)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="e77c9-618">Incorporación de un método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="e77c9-619">Agregue el siguiente método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="e77c9-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="e77c9-620">La respuesta de `DeleteTodoItem` es [204 (Sin contenido)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="e77c9-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="e77c9-621">Prueba del método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="e77c9-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="e77c9-622">Use Postman para eliminar una tarea pendiente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="e77c9-623">Establezca el método en `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="e77c9-624">Establezca el URI del objeto que quiera eliminar (por ejemplo, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="e77c9-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="e77c9-625">Seleccione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="e77c9-625">Select **Send**.</span></span>

<span data-ttu-id="e77c9-626">La aplicación de ejemplo permite eliminar todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="e77c9-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="e77c9-627">Sin embargo, al eliminar el último elemento, se creará uno nuevo en el constructor de clase de modelo la próxima vez que se llame a la API.</span><span class="sxs-lookup"><span data-stu-id="e77c9-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="e77c9-628">Llamar a la API web con JavaScript</span><span class="sxs-lookup"><span data-stu-id="e77c9-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="e77c9-629">En esta sección, se agrega una página HTML que usa JavaScript para llamar a la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="e77c9-630">JQuery inicia la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e77c9-630">jQuery initiates the request.</span></span> <span data-ttu-id="e77c9-631">JavaScript actualiza la página con los detalles de la respuesta de la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="e77c9-632">Configure la aplicación para [atender archivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) y [habilitar la asignación de archivos predeterminada](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) mediante la actualización de *Startup.cs* con el siguiente código resaltado:</span><span class="sxs-lookup"><span data-stu-id="e77c9-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="e77c9-633">Cree una carpeta *wwwroot* en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e77c9-634">Agregue un archivo HTML denominado *index.html* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e77c9-635">Reemplace el contenido por el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="e77c9-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e77c9-636">Agregue un archivo JavaScript denominado *site.js* al directorio *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e77c9-637">Reemplace el contenido por el siguiente código:</span><span class="sxs-lookup"><span data-stu-id="e77c9-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e77c9-638">Puede que sea necesario realizar un cambio en la configuración de inicio del proyecto de ASP.NET Core para probar la página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="e77c9-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e77c9-639">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e77c9-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e77c9-640">Quite la propiedad `launchUrl` para forzar a la aplicación a abrirse en *index.html*, esto es, el archivo predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e77c9-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e77c9-641">En este ejemplo se llama a todos los métodos CRUD de la API web.</span><span class="sxs-lookup"><span data-stu-id="e77c9-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="e77c9-642">A continuación, encontrará algunas explicaciones de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="e77c9-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="e77c9-643">Obtención de una lista de tareas pendientes</span><span class="sxs-lookup"><span data-stu-id="e77c9-643">Get a list of to-do items</span></span>

<span data-ttu-id="e77c9-644">jQuery envía una solicitud HTTP GET a la API web, que devuelve código JSON que representa una matriz de tareas pendientes.</span><span class="sxs-lookup"><span data-stu-id="e77c9-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e77c9-645">La función de devolución de llamada `success` se invoca si la solicitud se realiza correctamente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e77c9-646">En la devolución de llamada, el DOM se actualiza con la información de la tarea pendiente.</span><span class="sxs-lookup"><span data-stu-id="e77c9-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="e77c9-647">Incorporación de una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-647">Add a to-do item</span></span>

<span data-ttu-id="e77c9-648">jQuery envía una solicitud HTTP POST con la tarea pendiente en el cuerpo de dicha solicitud.</span><span class="sxs-lookup"><span data-stu-id="e77c9-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="e77c9-649">Las opciones `accepts` y `contentType` se establecen en `application/json` para especificar el tipo de medio que se va a recibir y a enviar.</span><span class="sxs-lookup"><span data-stu-id="e77c9-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e77c9-650">La tarea pendiente se convierte en JSON mediante [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="e77c9-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e77c9-651">Cuando la API devuelve un código de estado correcto, se invoca la función `getData` para actualizar la tabla HTML.</span><span class="sxs-lookup"><span data-stu-id="e77c9-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="e77c9-652">Actualizar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-652">Update a to-do item</span></span>

<span data-ttu-id="e77c9-653">El hecho de actualizar una tarea pendiente es similar al de agregar una.</span><span class="sxs-lookup"><span data-stu-id="e77c9-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e77c9-654">El valor `url` cambia para agregar el identificador único del elemento, y `type` es `PUT`.</span><span class="sxs-lookup"><span data-stu-id="e77c9-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="e77c9-655">Eliminar una tarea pendiente</span><span class="sxs-lookup"><span data-stu-id="e77c9-655">Delete a to-do item</span></span>

<span data-ttu-id="e77c9-656">Para eliminar una tarea pendiente, hay que establecer el valor `type` de la llamada de AJAX en `DELETE` y especificar el identificador único de la tarea en la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="e77c9-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="e77c9-657">Agregar compatibilidad con la autenticación a una API web</span><span class="sxs-lookup"><span data-stu-id="e77c9-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="e77c9-658">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e77c9-658">Additional resources</span></span>

<span data-ttu-id="e77c9-659">[Vea o descargue el código de ejemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="e77c9-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="e77c9-660">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e77c9-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="e77c9-661">Para obtener más información, vea los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="e77c9-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="e77c9-662">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="e77c9-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
