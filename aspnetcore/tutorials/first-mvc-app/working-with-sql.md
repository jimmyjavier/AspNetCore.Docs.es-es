---
title: Trabajo con SQL en una aplicación ASP.NET Core MVC
author: rick-anderson
description: Obtenga información sobre cómo usar SQL Server LocalDB o SQLite en una aplicación ASP.NET Core MVC.
ms.author: riande
ms.date: 8/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 54b10f10fb048819fced223f77f06a32102512d0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776212"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="38e67-103">Trabajo con SQL en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="38e67-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38e67-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38e67-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="38e67-105">El objeto `MvcMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38e67-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="38e67-106">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` del archivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38e67-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38e67-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38e67-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="38e67-108">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="38e67-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="38e67-109">Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="38e67-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="38e67-110">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="38e67-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="38e67-111">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="38e67-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="38e67-112">Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="38e67-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="38e67-113">Cuando la aplicación se implementa en un servidor de prueba o producción, se puede usar una variable de entorno para establecer la cadena de conexión en un servidor SQL Server de producción.</span><span class="sxs-lookup"><span data-stu-id="38e67-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="38e67-114">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="38e67-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38e67-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38e67-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="38e67-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="38e67-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="38e67-117">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="38e67-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="38e67-118">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="38e67-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="38e67-119">De forma predeterminada, la base de datos LocalDB crea archivos *.mdf* en el directorio *C:/Users/{usuario}* .</span><span class="sxs-lookup"><span data-stu-id="38e67-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="38e67-120">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="38e67-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](working-with-sql/_static/ssox.png)

* <span data-ttu-id="38e67-122">Haga clic con el botón derecho en la tabla `Movie` **> Diseñador de vistas**.</span><span class="sxs-lookup"><span data-stu-id="38e67-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](working-with-sql/_static/dv.png)

<span data-ttu-id="38e67-125">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="38e67-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="38e67-126">De forma predeterminada, EF convierte una propiedad denominada `ID` en la clave principal.</span><span class="sxs-lookup"><span data-stu-id="38e67-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="38e67-127">Haga clic con el botón derecho en la tabla `Movie` **> Ver datos**</span><span class="sxs-lookup"><span data-stu-id="38e67-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/ssox2.png)

  ![Tabla Movie abierta mostrando datos de la tabla](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="38e67-130">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="38e67-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="38e67-131">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="38e67-131">Seed the database</span></span>

<span data-ttu-id="38e67-132">Cree una nueva clase denominada `SeedData` en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="38e67-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="38e67-133">Reemplace el código generado con el siguiente:</span><span class="sxs-lookup"><span data-stu-id="38e67-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="38e67-134">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="38e67-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="38e67-135">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="38e67-135">Add the seed initializer</span></span>

<span data-ttu-id="38e67-136">Reemplace el contenido de *Program.cs* por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="38e67-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="38e67-137">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="38e67-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38e67-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38e67-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="38e67-139">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38e67-139">Delete all the records in the DB.</span></span> <span data-ttu-id="38e67-140">Puede hacerlo con los vínculos de eliminación en el explorador o desde SSOX.</span><span class="sxs-lookup"><span data-stu-id="38e67-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="38e67-141">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="38e67-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="38e67-142">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="38e67-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="38e67-143">Puede hacerlo con cualquiera de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="38e67-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="38e67-144">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**.</span><span class="sxs-lookup"><span data-stu-id="38e67-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Icono Bandeja del sistema de IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="38e67-147">Si está ejecutando VS en modo de no depuración, presione F5 para ejecutar en modo de depuración</span><span class="sxs-lookup"><span data-stu-id="38e67-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="38e67-148">Si está ejecutando VS en modo de depuración, detenga el depurador y presione F5</span><span class="sxs-lookup"><span data-stu-id="38e67-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="38e67-149">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="38e67-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="38e67-150">Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización).</span><span class="sxs-lookup"><span data-stu-id="38e67-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="38e67-151">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38e67-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="38e67-152">La aplicación muestra los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="38e67-152">The app shows the seeded data.</span></span>

![La aplicación Movie de MVC se abre en Microsoft Edge y muestra datos de la película](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="38e67-154">[Anterior](adding-model.md)
> [Siguiente](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="38e67-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38e67-155">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38e67-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="38e67-156">El objeto `MvcMovieContext` controla la tarea de conexión a la base de datos y asignación de objetos `Movie` a los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38e67-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="38e67-157">El contexto de base de datos se registra con el contenedor de [inserción de dependencias](xref:fundamentals/dependency-injection) en el método `ConfigureServices` del archivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38e67-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38e67-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38e67-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="38e67-159">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="38e67-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="38e67-160">Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="38e67-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="38e67-161">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="38e67-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="38e67-162">El sistema [Configuración](xref:fundamentals/configuration/index) de ASP.NET Core lee el elemento `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="38e67-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="38e67-163">Para el desarrollo local, obtiene la cadena de conexión del archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="38e67-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="38e67-164">Al implementar la aplicación en un servidor de producción o de prueba, puede usar una variable de entorno u otro enfoque para establecer la cadena de conexión en una instancia real de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="38e67-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="38e67-165">Para más información, vea [Configuración](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="38e67-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38e67-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38e67-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="38e67-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="38e67-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="38e67-168">LocalDB es una versión ligera del motor de base de datos de SQL Server Express dirigida al desarrollo de programas.</span><span class="sxs-lookup"><span data-stu-id="38e67-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="38e67-169">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="38e67-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="38e67-170">De forma predeterminada, la base de datos LocalDB crea archivos *.mdf* en el directorio *C:/Users/{usuario}* .</span><span class="sxs-lookup"><span data-stu-id="38e67-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="38e67-171">En el menú **Ver**, abra **Explorador de objetos de SQL Server** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="38e67-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menú Ver](working-with-sql/_static/ssox.png)

* <span data-ttu-id="38e67-173">Haga clic con el botón derecho en la tabla `Movie` **> Diseñador de vistas**.</span><span class="sxs-lookup"><span data-stu-id="38e67-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/design.png)

  ![Tabla Movie abierta en el diseñador](working-with-sql/_static/dv.png)

<span data-ttu-id="38e67-176">Observe el icono de llave junto a `ID`.</span><span class="sxs-lookup"><span data-stu-id="38e67-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="38e67-177">De forma predeterminada, EF convierte una propiedad denominada `ID` en la clave principal.</span><span class="sxs-lookup"><span data-stu-id="38e67-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="38e67-178">Haga clic con el botón derecho en la tabla `Movie` **> Ver datos**</span><span class="sxs-lookup"><span data-stu-id="38e67-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Menú contextual abierto en la tabla Movie](working-with-sql/_static/ssox2.png)

  ![Tabla Movie abierta mostrando datos de la tabla](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="38e67-181">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="38e67-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="38e67-182">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="38e67-182">Seed the database</span></span>

<span data-ttu-id="38e67-183">Cree una nueva clase denominada `SeedData` en la carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="38e67-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="38e67-184">Reemplace el código generado con el siguiente:</span><span class="sxs-lookup"><span data-stu-id="38e67-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="38e67-185">Si hay alguna película en la base de datos, se devuelve el inicializador y no se agrega ninguna película.</span><span class="sxs-lookup"><span data-stu-id="38e67-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="38e67-186">Agregar el inicializador</span><span class="sxs-lookup"><span data-stu-id="38e67-186">Add the seed initializer</span></span>

<span data-ttu-id="38e67-187">Reemplace el contenido de *Program.cs* por el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="38e67-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="38e67-188">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="38e67-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="38e67-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="38e67-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="38e67-190">Elimine todos los registros de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38e67-190">Delete all the records in the DB.</span></span> <span data-ttu-id="38e67-191">Puede hacerlo con los vínculos de eliminación en el explorador o desde SSOX.</span><span class="sxs-lookup"><span data-stu-id="38e67-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="38e67-192">Obligue a la aplicación a inicializarse (llame a los métodos de la clase `Startup`) para que se ejecute el método de inicialización.</span><span class="sxs-lookup"><span data-stu-id="38e67-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="38e67-193">Para forzar la inicialización, se debe detener y reiniciar IIS Express.</span><span class="sxs-lookup"><span data-stu-id="38e67-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="38e67-194">Puede hacerlo con cualquiera de los siguientes enfoques:</span><span class="sxs-lookup"><span data-stu-id="38e67-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="38e67-195">Haga clic con el botón derecho en el icono Bandeja del sistema de IIS Express del área de notificación y pulse en **Salir** o en **Detener sitio**.</span><span class="sxs-lookup"><span data-stu-id="38e67-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![Icono Bandeja del sistema de IIS Express](working-with-sql/_static/iisExIcon.png)

    ![Menú contextual](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="38e67-198">Si está ejecutando VS en modo de no depuración, presione F5 para ejecutar en modo de depuración</span><span class="sxs-lookup"><span data-stu-id="38e67-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="38e67-199">Si está ejecutando VS en modo de depuración, detenga el depurador y presione F5</span><span class="sxs-lookup"><span data-stu-id="38e67-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="38e67-200">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="38e67-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="38e67-201">Elimine todos los registros de la base de datos (para que se ejecute el método de inicialización).</span><span class="sxs-lookup"><span data-stu-id="38e67-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="38e67-202">Detenga e inicie la aplicación para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38e67-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="38e67-203">La aplicación muestra los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="38e67-203">The app shows the seeded data.</span></span>

![La aplicación Movie de MVC se abre en Microsoft Edge y muestra datos de la película](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="38e67-205">[Anterior](adding-model.md)
> [Siguiente](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="38e67-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
