---
title: Creación de una API Web con ASP.NET Core y MongoDB
author: prkhandelwal
description: En este tutorial se muestra cómo crear una API web de ASP.NET Core con una base de datos NoSQL de MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 4d1c2d915c646dd1c8fcadd25bcd420a0a749dc9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774774"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="7454e-103">Creación de una API Web con ASP.NET Core y MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="7454e-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) y [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7454e-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7454e-105">En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="7454e-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="7454e-106">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="7454e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7454e-107">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-107">Configure MongoDB</span></span>
> * <span data-ttu-id="7454e-108">Crear una base de datos de MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="7454e-109">Definir un esquema y una colección de MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="7454e-110">Realizar operaciones de CRUD de MongoDB desde una API web</span><span class="sxs-lookup"><span data-stu-id="7454e-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="7454e-111">Personalizar la serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="7454e-111">Customize JSON serialization</span></span>

<span data-ttu-id="7454e-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7454e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7454e-113">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7454e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7454e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7454e-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="7454e-115">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="7454e-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="7454e-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="7454e-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7454e-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7454e-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7454e-119">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="7454e-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7454e-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7454e-121">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="7454e-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7454e-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7454e-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="7454e-124">.NET Core SDK 3.0 o posterior</span><span class="sxs-lookup"><span data-stu-id="7454e-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7454e-125">Visual Studio para Mac, versión 7.7 o posterior</span><span class="sxs-lookup"><span data-stu-id="7454e-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="7454e-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="7454e-127">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-127">Configure MongoDB</span></span>

<span data-ttu-id="7454e-128">Si usa Windows, MongoDB está instalado en *C:\\Archivos de programa\\MongoDB* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7454e-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="7454e-129">Agregue *C:\\Archivos de programa\\MongoDB\\Server\\\<número_versión>\\bin* a la variable de entorno `Path`.</span><span class="sxs-lookup"><span data-stu-id="7454e-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="7454e-130">Este cambio permite el acceso a MongoDB desde cualquier lugar en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7454e-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="7454e-131">Use el Shell de mongo en los pasos siguientes para crear una base de datos, hacer colecciones y almacenar documentos.</span><span class="sxs-lookup"><span data-stu-id="7454e-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="7454e-132">Para obtener más información sobre los comandos de Shell de mongo, consulte [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Trabajo con el shell de Mongo).</span><span class="sxs-lookup"><span data-stu-id="7454e-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="7454e-133">Elija un directorio en el equipo de desarrollo para almacenar los datos.</span><span class="sxs-lookup"><span data-stu-id="7454e-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="7454e-134">Por ejemplo, *C:\\BooksData* en Windows.</span><span class="sxs-lookup"><span data-stu-id="7454e-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="7454e-135">Si no existe el directorio, créelo.</span><span class="sxs-lookup"><span data-stu-id="7454e-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="7454e-136">El shell de mongo no crea nuevos directorios.</span><span class="sxs-lookup"><span data-stu-id="7454e-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="7454e-137">Abra un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7454e-137">Open a command shell.</span></span> <span data-ttu-id="7454e-138">Ejecute el comando siguiente para conectarse a MongoDB en el puerto predeterminado 27017.</span><span class="sxs-lookup"><span data-stu-id="7454e-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="7454e-139">No olvide reemplazar `<data_directory_path>` por el directorio que eligió en el paso anterior.</span><span class="sxs-lookup"><span data-stu-id="7454e-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="7454e-140">Abra otra instancia del shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7454e-140">Open another command shell instance.</span></span> <span data-ttu-id="7454e-141">Conéctese a la base de datos de prueba de forma predeterminada ejecutando el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="7454e-142">Ejecute lo siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="7454e-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="7454e-143">Si aún no existe, se crea una base de datos denominada *BookstoreDb*.</span><span class="sxs-lookup"><span data-stu-id="7454e-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="7454e-144">Si la base de datos existe, su conexión se abre para las transacciones.</span><span class="sxs-lookup"><span data-stu-id="7454e-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="7454e-145">Cree una colección `Books` con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="7454e-146">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7454e-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="7454e-147">Defina un esquema para la colección `Books` e inserte dos documentos con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="7454e-148">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7454e-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="7454e-149">Los identificadores que se muestran en este artículo no coinciden con los que se mostrarán cuando ejecute este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7454e-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="7454e-150">Vea los documentos en la base de datos mediante el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="7454e-151">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7454e-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="7454e-152">El esquema agrega una propiedad `_id` generada automáticamente del tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="7454e-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="7454e-153">La base de datos está lista.</span><span class="sxs-lookup"><span data-stu-id="7454e-153">The database is ready.</span></span> <span data-ttu-id="7454e-154">Puede empezar a crear la API web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7454e-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="7454e-155">Creación de un proyecto de API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7454e-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7454e-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7454e-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7454e-157">Vaya a **Archivo** > **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7454e-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7454e-158">Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7454e-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="7454e-159">Denomine el proyecto *BooksApi* y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7454e-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="7454e-160">Seleccione **.NET Core** como plataforma de destino y **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="7454e-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="7454e-161">Seleccione la plantilla de proyecto **API** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7454e-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="7454e-162">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7454e-163">En la ventana **Consola del Administrador de paquetes**, desplácese hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="7454e-164">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7454e-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7454e-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7454e-166">Ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="7454e-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="7454e-167">Se genera un nuevo proyecto de API web de ASP.NET Core destinado a .NET Core, que puede abrir en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7454e-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="7454e-168">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo en el que se le indicará que **faltan los activos necesarios para compilar y depurar en "RazonPagesMovie" y, luego, si quiere agregarlos**.</span><span class="sxs-lookup"><span data-stu-id="7454e-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="7454e-169">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7454e-169">Select **Yes**.</span></span>
1. <span data-ttu-id="7454e-170">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7454e-171">Abra **Terminal integrado** y navegue hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="7454e-172">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7454e-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7454e-173">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7454e-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7454e-174">Vaya a **Archivo** > **Nueva solución** > **.NET Core** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="7454e-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="7454e-175">Seleccione la plantilla de proyecto de C# **API web ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7454e-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="7454e-176">Seleccione **.NET Core 3.0** en la lista desplegable **Plataforma de destino** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7454e-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="7454e-177">Escriba *BooksApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7454e-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="7454e-178">En el panel **Explorador de soluciones**, haga clic con el botón derecho en el nodo **Dependencias** del proyecto y seleccione **Agregar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="7454e-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="7454e-179">Escriba *MongoDB.Driver* en el cuadro de búsqueda, seleccione el paquete *MongoDB.Driver* y, luego, **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="7454e-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="7454e-180">Seleccione el botón **Aceptar** del cuadro de diálogo **Aceptación de la licencia**.</span><span class="sxs-lookup"><span data-stu-id="7454e-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="7454e-181">Adición de un modelo de entidad</span><span class="sxs-lookup"><span data-stu-id="7454e-181">Add an entity model</span></span>

1. <span data-ttu-id="7454e-182">Agregue un directorio *Modelos* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="7454e-183">Agregue una clase `Book` al directorio *Modelos* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="7454e-184">En la clase anterior, se requiere la propiedad `Id`</span><span class="sxs-lookup"><span data-stu-id="7454e-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="7454e-185">para asignar el objeto de Common Language Runtime (CLR) a la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="7454e-186">para anotarla con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) a fin de designarla como clave principal del documento.</span><span class="sxs-lookup"><span data-stu-id="7454e-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="7454e-187">para anotarla con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) a fin de permitir que se pase el parámetro como tipo `string` en lugar de una estructura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="7454e-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="7454e-188">Mongo controla la conversión de `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="7454e-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="7454e-189">La propiedad `BookName` se anota con el atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="7454e-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="7454e-190">El valor `Name` del atributo representa el nombre de propiedad en la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="7454e-191">Adición de un modelo configuración</span><span class="sxs-lookup"><span data-stu-id="7454e-191">Add a configuration model</span></span>

1. <span data-ttu-id="7454e-192">Agregue los siguientes valores de configuración de base de datos a *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7454e-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="7454e-193">Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="7454e-194">La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7454e-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="7454e-195">Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.</span><span class="sxs-lookup"><span data-stu-id="7454e-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="7454e-196">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7454e-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="7454e-197">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="7454e-197">In the preceding code:</span></span>

   * <span data-ttu-id="7454e-198">La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="7454e-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="7454e-199">Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7454e-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="7454e-200">La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="7454e-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7454e-201">Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="7454e-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="7454e-202">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="7454e-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="7454e-203">Adición de un servicio de operaciones CRUD</span><span class="sxs-lookup"><span data-stu-id="7454e-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="7454e-204">Agregue un directorio *Servicios* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="7454e-205">Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="7454e-206">En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor.</span><span class="sxs-lookup"><span data-stu-id="7454e-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="7454e-207">Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="7454e-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="7454e-208">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7454e-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="7454e-209">En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo.</span><span class="sxs-lookup"><span data-stu-id="7454e-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="7454e-210">La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="7454e-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="7454e-211">Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="7454e-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="7454e-212">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="7454e-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="7454e-213">La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="7454e-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="7454e-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7454e-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="7454e-215">Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:</span><span class="sxs-lookup"><span data-stu-id="7454e-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="7454e-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa la base de datos de Mongo para realizar operaciones.</span><span class="sxs-lookup"><span data-stu-id="7454e-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="7454e-217">Este tutorial usa el método genérico [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) en la interfaz para tener acceso a los datos de una colección específica.</span><span class="sxs-lookup"><span data-stu-id="7454e-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="7454e-218">Realice las operaciones CRUD en la colección después de llamar a este método.</span><span class="sxs-lookup"><span data-stu-id="7454e-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="7454e-219">En la llamada al método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="7454e-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="7454e-220">`collection` representa el nombre de la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="7454e-221">`TDocument` representa el tipo de objeto CLR almacenado en la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="7454e-222">`GetCollection<TDocument>(collection)` devuelve un objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="7454e-223">En este tutorial, se invocan los métodos siguientes en la colección:</span><span class="sxs-lookup"><span data-stu-id="7454e-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="7454e-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): elimina un único documento que cumpla los criterios de búsqueda proporcionados.</span><span class="sxs-lookup"><span data-stu-id="7454e-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="7454e-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): devuelve todos los documentos de la colección que cumplen los criterios de búsqueda indicados.</span><span class="sxs-lookup"><span data-stu-id="7454e-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="7454e-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserta el objeto proporcionado como un nuevo documento en la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="7454e-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): reemplaza un único documento que cumpla los criterios de búsqueda indicados por el objeto proporcionado.</span><span class="sxs-lookup"><span data-stu-id="7454e-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7454e-228">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="7454e-228">Add a controller</span></span>

<span data-ttu-id="7454e-229">Agregue una clase `BooksController` al directorio *Controladores* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="7454e-230">El controlador de API web anterior:</span><span class="sxs-lookup"><span data-stu-id="7454e-230">The preceding web API controller:</span></span>

* <span data-ttu-id="7454e-231">Usa la clase `BookService` para realizar operaciones CRUD.</span><span class="sxs-lookup"><span data-stu-id="7454e-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="7454e-232">Contiene métodos de acción para admitir las solicitudes GET, POST, PUT y DELETE de HTTP.</span><span class="sxs-lookup"><span data-stu-id="7454e-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="7454e-233">Llama a <xref:System.Web.Http.ApiController.CreatedAtRoute*> en el método de acción `Create` para devolver una respuesta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7454e-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="7454e-234">El código de estado 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7454e-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="7454e-235">`CreatedAtRoute` también agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="7454e-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="7454e-236">El encabezado `Location` especifica el identificador URI del libro recién creado.</span><span class="sxs-lookup"><span data-stu-id="7454e-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="7454e-237">Prueba de la API web</span><span class="sxs-lookup"><span data-stu-id="7454e-237">Test the web API</span></span>

1. <span data-ttu-id="7454e-238">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7454e-238">Build and run the app.</span></span>

1. <span data-ttu-id="7454e-239">Vaya a `http://localhost:<port>/api/books` para probar el método de acción `Get` sin parámetros del controlador.</span><span class="sxs-lookup"><span data-stu-id="7454e-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="7454e-240">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="7454e-240">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="7454e-241">Vaya a `http://localhost:<port>/api/books/{id here}` para probar el método de acción `Get` sobrecargado del controlador.</span><span class="sxs-lookup"><span data-stu-id="7454e-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="7454e-242">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="7454e-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="7454e-243">Configuración de las opciones de serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="7454e-243">Configure JSON serialization options</span></span>

<span data-ttu-id="7454e-244">Hay dos detalles que cambiar sobre las respuestas JSON devueltas en la sección [Prueba de la API web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="7454e-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="7454e-245">Las mayúsculas y minúsculas Camel predeterminadas de los nombres de propiedad se deben cambiar para que coincidan con el uso de mayúsculas y minúsculas de Pascal de los nombres de propiedad del objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="7454e-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="7454e-246">La propiedad `bookName` se debe devolver como `Name`.</span><span class="sxs-lookup"><span data-stu-id="7454e-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="7454e-247">Para satisfacer los requisitos anteriores, realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="7454e-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="7454e-248">JSON.NET se ha quitado de la plataforma compartida de ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7454e-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="7454e-249">Agregue una referencia de paquete a [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="7454e-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="7454e-250">En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="7454e-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="7454e-251">Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="7454e-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="7454e-252">Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.</span><span class="sxs-lookup"><span data-stu-id="7454e-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="7454e-253">En *Models/Book.cs*, anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="7454e-254">El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.</span><span class="sxs-lookup"><span data-stu-id="7454e-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="7454e-255">Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="7454e-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="7454e-256">Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="7454e-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="7454e-257">Observe la diferencia en los nombres de propiedad JSON.</span><span class="sxs-lookup"><span data-stu-id="7454e-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7454e-258">En este tutorial se crea una API web que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) en una base de datos NoSQL de [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="7454e-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="7454e-259">En este tutorial aprenderá a:</span><span class="sxs-lookup"><span data-stu-id="7454e-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7454e-260">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-260">Configure MongoDB</span></span>
> * <span data-ttu-id="7454e-261">Crear una base de datos de MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="7454e-262">Definir un esquema y una colección de MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="7454e-263">Realizar operaciones de CRUD de MongoDB desde una API web</span><span class="sxs-lookup"><span data-stu-id="7454e-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="7454e-264">Personalizar la serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="7454e-264">Customize JSON serialization</span></span>

<span data-ttu-id="7454e-265">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7454e-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7454e-266">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7454e-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7454e-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7454e-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="7454e-268">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="7454e-268">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="7454e-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) con la carga de trabajo **ASP.NET y desarrollo web**</span><span class="sxs-lookup"><span data-stu-id="7454e-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7454e-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7454e-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7454e-272">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="7454e-272">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7454e-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7454e-274">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="7454e-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7454e-276">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7454e-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="7454e-277">SDK de .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="7454e-277">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7454e-278">Visual Studio para Mac, versión 7.7 o posterior</span><span class="sxs-lookup"><span data-stu-id="7454e-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="7454e-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="7454e-280">Configurar MongoDB</span><span class="sxs-lookup"><span data-stu-id="7454e-280">Configure MongoDB</span></span>

<span data-ttu-id="7454e-281">Si usa Windows, MongoDB está instalado en *C:\\Archivos de programa\\MongoDB* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="7454e-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="7454e-282">Agregue *C:\\Archivos de programa\\MongoDB\\Server\\\<número_versión>\\bin* a la variable de entorno `Path`.</span><span class="sxs-lookup"><span data-stu-id="7454e-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="7454e-283">Este cambio permite el acceso a MongoDB desde cualquier lugar en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="7454e-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="7454e-284">Use el Shell de mongo en los pasos siguientes para crear una base de datos, hacer colecciones y almacenar documentos.</span><span class="sxs-lookup"><span data-stu-id="7454e-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="7454e-285">Para obtener más información sobre los comandos de Shell de mongo, consulte [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell) (Trabajo con el shell de Mongo).</span><span class="sxs-lookup"><span data-stu-id="7454e-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="7454e-286">Elija un directorio en el equipo de desarrollo para almacenar los datos.</span><span class="sxs-lookup"><span data-stu-id="7454e-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="7454e-287">Por ejemplo, *C:\\BooksData* en Windows.</span><span class="sxs-lookup"><span data-stu-id="7454e-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="7454e-288">Si no existe el directorio, créelo.</span><span class="sxs-lookup"><span data-stu-id="7454e-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="7454e-289">El shell de mongo no crea nuevos directorios.</span><span class="sxs-lookup"><span data-stu-id="7454e-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="7454e-290">Abra un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7454e-290">Open a command shell.</span></span> <span data-ttu-id="7454e-291">Ejecute el comando siguiente para conectarse a MongoDB en el puerto predeterminado 27017.</span><span class="sxs-lookup"><span data-stu-id="7454e-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="7454e-292">No olvide reemplazar `<data_directory_path>` por el directorio que eligió en el paso anterior.</span><span class="sxs-lookup"><span data-stu-id="7454e-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="7454e-293">Abra otra instancia del shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="7454e-293">Open another command shell instance.</span></span> <span data-ttu-id="7454e-294">Conéctese a la base de datos de prueba de forma predeterminada ejecutando el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="7454e-295">Ejecute lo siguiente en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="7454e-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="7454e-296">Si aún no existe, se crea una base de datos denominada *BookstoreDb*.</span><span class="sxs-lookup"><span data-stu-id="7454e-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="7454e-297">Si la base de datos existe, su conexión se abre para las transacciones.</span><span class="sxs-lookup"><span data-stu-id="7454e-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="7454e-298">Cree una colección `Books` con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="7454e-299">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7454e-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="7454e-300">Defina un esquema para la colección `Books` e inserte dos documentos con el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="7454e-301">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7454e-301">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="7454e-302">Los identificadores que se muestran en este artículo no coinciden con los que se mostrarán cuando ejecute este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="7454e-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="7454e-303">Vea los documentos en la base de datos mediante el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="7454e-304">Se muestra el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="7454e-304">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="7454e-305">El esquema agrega una propiedad `_id` generada automáticamente del tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="7454e-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="7454e-306">La base de datos está lista.</span><span class="sxs-lookup"><span data-stu-id="7454e-306">The database is ready.</span></span> <span data-ttu-id="7454e-307">Puede empezar a crear la API web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7454e-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="7454e-308">Creación de un proyecto de API web de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7454e-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7454e-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7454e-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7454e-310">Vaya a **Archivo** > **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="7454e-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7454e-311">Seleccione el tipo de proyecto **Aplicación web de ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7454e-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="7454e-312">Denomine el proyecto *BooksApi* y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7454e-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="7454e-313">Seleccione el marco de destino **.NET Core** y **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="7454e-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="7454e-314">Seleccione la plantilla de proyecto **API** y, luego, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7454e-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="7454e-315">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7454e-316">En la ventana **Consola del Administrador de paquetes**, desplácese hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="7454e-317">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7454e-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7454e-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7454e-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7454e-319">Ejecute los siguientes comandos en un shell de comandos:</span><span class="sxs-lookup"><span data-stu-id="7454e-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="7454e-320">Se genera un nuevo proyecto de API web de ASP.NET Core destinado a .NET Core, que puede abrir en Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7454e-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="7454e-321">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo en el que se le indicará que **faltan los activos necesarios para compilar y depurar en "RazonPagesMovie" y, luego, si quiere agregarlos**.</span><span class="sxs-lookup"><span data-stu-id="7454e-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="7454e-322">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="7454e-322">Select **Yes**.</span></span>
1. <span data-ttu-id="7454e-323">Visite la [galería de NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar la última versión estable del controlador .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7454e-324">Abra **Terminal integrado** y navegue hasta la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="7454e-325">Ejecute el siguiente comando para instalar el controlador .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7454e-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7454e-326">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7454e-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7454e-327">Vaya a **Archivo** > **Nueva solución** > **.NET Core** > **Aplicación**.</span><span class="sxs-lookup"><span data-stu-id="7454e-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="7454e-328">Seleccione la plantilla de proyecto de C# **API web ASP.NET Core** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7454e-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="7454e-329">Seleccione **.NET Core 2.2** en la lista desplegable **Plataforma de destino** y, luego, **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="7454e-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="7454e-330">Escriba *BooksApi* en **Nombre del proyecto** y seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="7454e-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="7454e-331">En el panel **Explorador de soluciones**, haga clic con el botón derecho en el nodo **Dependencias** del proyecto y seleccione **Agregar paquetes**.</span><span class="sxs-lookup"><span data-stu-id="7454e-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="7454e-332">Escriba *MongoDB.Driver* en el cuadro de búsqueda, seleccione el paquete *MongoDB.Driver* y, luego, **Agregar paquete**.</span><span class="sxs-lookup"><span data-stu-id="7454e-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="7454e-333">Seleccione el botón **Aceptar** del cuadro de diálogo **Aceptación de la licencia**.</span><span class="sxs-lookup"><span data-stu-id="7454e-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="7454e-334">Adición de un modelo de entidad</span><span class="sxs-lookup"><span data-stu-id="7454e-334">Add an entity model</span></span>

1. <span data-ttu-id="7454e-335">Agregue un directorio *Modelos* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="7454e-336">Agregue una clase `Book` al directorio *Modelos* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="7454e-337">En la clase anterior, se requiere la propiedad `Id`</span><span class="sxs-lookup"><span data-stu-id="7454e-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="7454e-338">para asignar el objeto de Common Language Runtime (CLR) a la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="7454e-339">para anotarla con [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) a fin de designarla como clave principal del documento.</span><span class="sxs-lookup"><span data-stu-id="7454e-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="7454e-340">para anotarla con [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) a fin de permitir que se pase el parámetro como tipo `string` en lugar de una estructura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="7454e-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="7454e-341">Mongo controla la conversión de `string` a `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="7454e-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="7454e-342">La propiedad `BookName` se anota con el atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="7454e-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="7454e-343">El valor `Name` del atributo representa el nombre de propiedad en la colección de MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7454e-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="7454e-344">Adición de un modelo configuración</span><span class="sxs-lookup"><span data-stu-id="7454e-344">Add a configuration model</span></span>

1. <span data-ttu-id="7454e-345">Agregue los siguientes valores de configuración de base de datos a *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7454e-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="7454e-346">Agregue un archivo *BookstoreDatabaseSettings.cs* al directorio *Models* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="7454e-347">La clase anterior `BookstoreDatabaseSettings` se utiliza para almacenar los valores de propiedad `BookstoreDatabaseSettings` del archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7454e-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="7454e-348">Los nombres de las propiedades de JSON y C# son iguales para facilitar el proceso de asignación.</span><span class="sxs-lookup"><span data-stu-id="7454e-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="7454e-349">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7454e-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="7454e-350">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="7454e-350">In the preceding code:</span></span>

   * <span data-ttu-id="7454e-351">La instancia de configuración a la que la sección `BookstoreDatabaseSettings` del archivo *appsettings.json* enlaza está registrada en el contenedor de inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="7454e-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="7454e-352">Por ejemplo, una propiedad `ConnectionString` del objeto `BookstoreDatabaseSettings` se rellena con la propiedad `BookstoreDatabaseSettings:ConnectionString` en *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7454e-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="7454e-353">La interfaz `IBookstoreDatabaseSettings` se registra en la inserción de dependencias con una [duración de servicio](xref:fundamentals/dependency-injection#service-lifetimes) de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="7454e-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7454e-354">Cuando se inserta, la instancia de la interfaz se resuelve en un objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="7454e-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="7454e-355">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver las referencias a `BookstoreDatabaseSettings` y `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="7454e-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="7454e-356">Adición de un servicio de operaciones CRUD</span><span class="sxs-lookup"><span data-stu-id="7454e-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="7454e-357">Agregue un directorio *Servicios* a la raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="7454e-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="7454e-358">Agregue una clase `BookService` al directorio *Servicios* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="7454e-359">En el código anterior, se recuperó una instancia de `IBookstoreDatabaseSettings` de la inserción de dependencias mediante la inserción de un constructor.</span><span class="sxs-lookup"><span data-stu-id="7454e-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="7454e-360">Esta técnica proporciona acceso a los valores de configuración de *appsettings.json* que se agregaron en la sección [Adición de un modelo de configuración](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="7454e-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="7454e-361">Agregue el código resaltado siguiente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7454e-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="7454e-362">En el código anterior, la clase `BookService` se registra con inserción de dependencias para admitir la inserción del constructor en las clases de consumo.</span><span class="sxs-lookup"><span data-stu-id="7454e-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="7454e-363">La duración de servicio de tipo singleton es la más adecuada porque `BookService` toma una dependencia directa sobre `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="7454e-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="7454e-364">Según las [instrucciones oficiales de reutilización de cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` debe registrarse en la inserción de dependencias con una duración de servicio de tipo singleton.</span><span class="sxs-lookup"><span data-stu-id="7454e-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="7454e-365">Agregue el código siguiente en la parte superior del archivo *Startup.cs* para resolver la referencia a `BookService`:</span><span class="sxs-lookup"><span data-stu-id="7454e-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="7454e-366">La clase`BookService` usa los miembros `MongoDB.Driver` siguientes para realizar operaciones CRUD en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="7454e-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="7454e-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): lee la instancia del servidor para realizar operaciones de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7454e-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="7454e-368">Se proporciona la cadena de conexión de MongoDB al constructor de esta clase:</span><span class="sxs-lookup"><span data-stu-id="7454e-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="7454e-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): representa la base de datos de Mongo para realizar operaciones.</span><span class="sxs-lookup"><span data-stu-id="7454e-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="7454e-370">Este tutorial usa el método genérico [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) en la interfaz para tener acceso a los datos de una colección específica.</span><span class="sxs-lookup"><span data-stu-id="7454e-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="7454e-371">Realice las operaciones CRUD en la colección después de llamar a este método.</span><span class="sxs-lookup"><span data-stu-id="7454e-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="7454e-372">En la llamada al método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="7454e-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="7454e-373">`collection` representa el nombre de la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="7454e-374">`TDocument` representa el tipo de objeto CLR almacenado en la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="7454e-375">`GetCollection<TDocument>(collection)` devuelve un objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="7454e-376">En este tutorial, se invocan los métodos siguientes en la colección:</span><span class="sxs-lookup"><span data-stu-id="7454e-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="7454e-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): elimina un único documento que cumpla los criterios de búsqueda proporcionados.</span><span class="sxs-lookup"><span data-stu-id="7454e-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="7454e-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): devuelve todos los documentos de la colección que cumplen los criterios de búsqueda indicados.</span><span class="sxs-lookup"><span data-stu-id="7454e-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="7454e-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): inserta el objeto proporcionado como un nuevo documento en la colección.</span><span class="sxs-lookup"><span data-stu-id="7454e-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="7454e-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): reemplaza un único documento que cumpla los criterios de búsqueda indicados por el objeto proporcionado.</span><span class="sxs-lookup"><span data-stu-id="7454e-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7454e-381">Incorporación de un controlador</span><span class="sxs-lookup"><span data-stu-id="7454e-381">Add a controller</span></span>

<span data-ttu-id="7454e-382">Agregue una clase `BooksController` al directorio *Controladores* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="7454e-383">El controlador de API web anterior:</span><span class="sxs-lookup"><span data-stu-id="7454e-383">The preceding web API controller:</span></span>

* <span data-ttu-id="7454e-384">Usa la clase `BookService` para realizar operaciones CRUD.</span><span class="sxs-lookup"><span data-stu-id="7454e-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="7454e-385">Contiene métodos de acción para admitir las solicitudes GET, POST, PUT y DELETE de HTTP.</span><span class="sxs-lookup"><span data-stu-id="7454e-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="7454e-386">Llama a <xref:System.Web.Http.ApiController.CreatedAtRoute*> en el método de acción `Create` para devolver una respuesta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7454e-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="7454e-387">El código de estado 201 es la respuesta estándar para un método HTTP POST que crea un recurso en el servidor.</span><span class="sxs-lookup"><span data-stu-id="7454e-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="7454e-388">`CreatedAtRoute` también agrega un encabezado `Location` a la respuesta.</span><span class="sxs-lookup"><span data-stu-id="7454e-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="7454e-389">El encabezado `Location` especifica el identificador URI del libro recién creado.</span><span class="sxs-lookup"><span data-stu-id="7454e-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="7454e-390">Prueba de la API web</span><span class="sxs-lookup"><span data-stu-id="7454e-390">Test the web API</span></span>

1. <span data-ttu-id="7454e-391">Compile y ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7454e-391">Build and run the app.</span></span>

1. <span data-ttu-id="7454e-392">Vaya a `http://localhost:<port>/api/books` para probar el método de acción `Get` sin parámetros del controlador.</span><span class="sxs-lookup"><span data-stu-id="7454e-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="7454e-393">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="7454e-393">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="7454e-394">Vaya a `http://localhost:<port>/api/books/{id here}` para probar el método de acción `Get` sobrecargado del controlador.</span><span class="sxs-lookup"><span data-stu-id="7454e-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="7454e-395">Se muestra la siguiente respuesta JSON:</span><span class="sxs-lookup"><span data-stu-id="7454e-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="7454e-396">Configuración de las opciones de serialización de JSON</span><span class="sxs-lookup"><span data-stu-id="7454e-396">Configure JSON serialization options</span></span>

<span data-ttu-id="7454e-397">Hay dos detalles que cambiar sobre las respuestas JSON devueltas en la sección [Prueba de la API web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="7454e-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="7454e-398">Las mayúsculas y minúsculas Camel predeterminadas de los nombres de propiedad se deben cambiar para que coincidan con el uso de mayúsculas y minúsculas de Pascal de los nombres de propiedad del objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="7454e-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="7454e-399">La propiedad `bookName` se debe devolver como `Name`.</span><span class="sxs-lookup"><span data-stu-id="7454e-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="7454e-400">Para satisfacer los requisitos anteriores, realice los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="7454e-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="7454e-401">En `Startup.ConfigureServices`, cambie el código resaltado siguiente en la llamada al método `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="7454e-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="7454e-402">Con el cambio anterior, los nombres de propiedad de la respuesta JSON serializada de la API web coinciden con sus nombres de propiedad correspondientes en el tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="7454e-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="7454e-403">Por ejemplo, la propiedad `Author` de la clase `Book` se serializa como `Author`.</span><span class="sxs-lookup"><span data-stu-id="7454e-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="7454e-404">En *Models/Book.cs*, anote la propiedad `BookName` con el atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) siguiente:</span><span class="sxs-lookup"><span data-stu-id="7454e-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="7454e-405">El valor `Name` del atributo `[JsonProperty]` representa el nombre de propiedad en la respuesta JSON serializada de la API web.</span><span class="sxs-lookup"><span data-stu-id="7454e-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="7454e-406">Agregue el código siguiente en la parte superior del archivo *Models/Book.cs* para resolver la referencia al atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="7454e-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="7454e-407">Repita los pasos definidos en la sección [Prueba de la API web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="7454e-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="7454e-408">Observe la diferencia en los nombres de propiedad JSON.</span><span class="sxs-lookup"><span data-stu-id="7454e-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="7454e-409">Agregar compatibilidad con la autenticación a una API web</span><span class="sxs-lookup"><span data-stu-id="7454e-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="7454e-410">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="7454e-410">Next steps</span></span>

<span data-ttu-id="7454e-411">Para obtener más información sobre la creación de las API web de ASP.NET Core, consulte los siguientes recursos:</span><span class="sxs-lookup"><span data-stu-id="7454e-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="7454e-412">Versión de YouTube de este artículo</span><span class="sxs-lookup"><span data-stu-id="7454e-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
