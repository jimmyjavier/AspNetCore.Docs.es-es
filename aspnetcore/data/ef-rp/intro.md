---
<span data-ttu-id="f2507-101">title: Razor Pages con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8 author: rick-anderson description: Aquí se muestra cómo crear una aplicación de Razor Pages con Entity Framework Core ms.author: riande ms.custom: "mvc, seodec18" ms.date: 09/26/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/intro</span><span class="sxs-lookup"><span data-stu-id="f2507-101">title: Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8 author: rick-anderson description: Shows how to create a Razor Pages app using Entity Framework Core ms.author: riande ms.custom: "mvc, seodec18" ms.date: 09/26/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/intro</span></span>
---

# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="f2507-102">Páginas de Razor con Entity Framework Core en ASP.NET Core: Tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="f2507-102">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="f2507-103">Por [Tom Dykstra](https://github.com/tdykstra) y [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2507-103">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f2507-104">Este es el primero de una serie de tutoriales en los que se muestra cómo usar Entity Framework (EF) Core en una aplicación [Razor Pages en ASP.NET Core](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="f2507-104">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="f2507-105">En el tutorial se crea un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f2507-105">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f2507-106">El sitio incluye funciones como la admisión de alumnos, la creación de cursos y las asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="f2507-106">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f2507-107">En el tutorial se usa el enfoque de Code First.</span><span class="sxs-lookup"><span data-stu-id="f2507-107">The tutorial uses the code first approach.</span></span> <span data-ttu-id="f2507-108">Para obtener información sobre cómo seguir este tutorial mediante el enfoque de Database First, consulte [este problema de GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="f2507-108">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="f2507-109">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="f2507-109">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f2507-110">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f2507-110">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f2507-111">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="f2507-111">Prerequisites</span></span>

* <span data-ttu-id="f2507-112">Si no está familiarizado con Razor Pages, consulte la serie de tutoriales [Introducción a Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de empezar este.</span><span class="sxs-lookup"><span data-stu-id="f2507-112">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-113">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-114">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-114">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="f2507-115">Motores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-115">Database engines</span></span>

<span data-ttu-id="f2507-116">En las instrucciones de Visual Studio se usa [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), una versión de SQL Server Express que solo se ejecuta en Windows.</span><span class="sxs-lookup"><span data-stu-id="f2507-116">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="f2507-117">En las instrucciones de Visual Studio Code se usa [SQLite](https://www.sqlite.org/), un motor de base de datos multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="f2507-117">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="f2507-118">Si decide usar SQLite, descargue e instale una herramienta de terceros para administrar y ver una base de datos de SQLite, como [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="f2507-118">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f2507-119">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="f2507-119">Troubleshooting</span></span>

<span data-ttu-id="f2507-120">Si experimenta un problema que no puede resolver, compare el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f2507-120">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f2507-121">Una buena forma de obtener ayuda consiste en publicar una pregunta en StackOverflow.com con la [etiqueta ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o la [etiqueta EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="f2507-121">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="f2507-122">La aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="f2507-122">The sample app</span></span>

<span data-ttu-id="f2507-123">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="f2507-123">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="f2507-124">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="f2507-124">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f2507-125">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="f2507-125">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index30.png)

![Página de edición de estudiantes](intro/_static/student-edit30.png)

<span data-ttu-id="f2507-128">El estilo de la interfaz de usuario de este sitio se basa en las plantillas de proyecto integradas.</span><span class="sxs-lookup"><span data-stu-id="f2507-128">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="f2507-129">El enfoque del tutorial es cómo usar EF Core, no cómo personalizar la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="f2507-129">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="f2507-130">Siga el vínculo de la parte superior de la página para obtener el código fuente para el proyecto completado.</span><span class="sxs-lookup"><span data-stu-id="f2507-130">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="f2507-131">La carpeta *cu30* contiene el código para la versión ASP.NET Core 3.0 del tutorial.</span><span class="sxs-lookup"><span data-stu-id="f2507-131">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="f2507-132">Los archivos que reflejan el estado del código para los tutoriales 1-7 se pueden encontrar en la carpeta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="f2507-132">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2507-134">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="f2507-134">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="f2507-135">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-135">Build the project.</span></span>
* <span data-ttu-id="f2507-136">En la Consola del administrador de paquetes (PMC), ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="f2507-137">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2507-139">Para ejecutar la aplicación después de descargar el proyecto completado:</span><span class="sxs-lookup"><span data-stu-id="f2507-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="f2507-140">Elimine *ContosoUniversity.csproj* y cambie el nombre de *ContosoUniversitySQLite.csproj* por *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f2507-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="f2507-141">Elimine *Startup.cs*, y cambie el nombre de *StartupSQLite.cs* por *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f2507-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="f2507-142">Elimine *appSettings.json* y cambie el nombre de *appSettingsSQLite.json* por *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f2507-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="f2507-143">Elimine la carpeta *Migrations* y cambie el nombre de *MigrationsSQL* por *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="f2507-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="f2507-144">Realice una búsqueda global `#if SQLiteVersion` y quite `#if SQLiteVersion` y la instrucción `#endif` asociada.</span><span class="sxs-lookup"><span data-stu-id="f2507-144">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="f2507-145">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-145">Build the project.</span></span>
* <span data-ttu-id="f2507-146">En un símbolo del sistema en la carpeta del proyecto, ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="f2507-146">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="f2507-147">En la herramienta SQLite, ejecute la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="f2507-147">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="f2507-148">Ejecute el proyecto para inicializar la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-148">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="f2507-149">Creación del proyecto de aplicación web</span><span class="sxs-lookup"><span data-stu-id="f2507-149">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2507-151">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="f2507-151">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f2507-152">Seleccione **Aplicación web de ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f2507-152">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f2507-153">Asigne el nombre *ContosoUniversity* al proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-153">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="f2507-154">Es importante usar este nombre exacto incluido el uso de mayúsculas, para que los espacios de nombres coincidan cuando se copie y pegue el código.</span><span class="sxs-lookup"><span data-stu-id="f2507-154">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="f2507-155">Seleccione **.NET Core** y **ASP.NET Core 3.0** en las listas desplegables y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="f2507-155">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-156">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2507-157">En un terminal, vaya a la carpeta en la que se debe crear la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-157">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="f2507-158">Ejecute los comandos siguientes para crear un proyecto de Razor Pages y `cd` para acceder a la nueva carpeta del proyecto:</span><span class="sxs-lookup"><span data-stu-id="f2507-158">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f2507-159">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="f2507-159">Set up the site style</span></span>

<span data-ttu-id="f2507-160">Configure el encabezado, el pie de página y el menú del sitio mediante la actualización de *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f2507-160">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="f2507-161">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="f2507-161">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f2507-162">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="f2507-162">There are three occurrences.</span></span>

* <span data-ttu-id="f2507-163">Elimine las entradas de menú **Home** y **Privacy**, y agregue entradas para **About**, **Students**, **Courses**, **Instructors** y **Departments**.</span><span class="sxs-lookup"><span data-stu-id="f2507-163">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="f2507-164">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="f2507-164">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="f2507-165">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET Core con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="f2507-165">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="f2507-166">Ejecute la aplicación para comprobar que aparece la página principal.</span><span class="sxs-lookup"><span data-stu-id="f2507-166">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="f2507-167">El modelo de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-167">The data model</span></span>

<span data-ttu-id="f2507-168">En las secciones siguientes se crea un modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="f2507-168">The following sections create a data model:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="f2507-170">Un alumno se puede inscribir en cualquier número de cursos y un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="f2507-170">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="f2507-171">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="f2507-171">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

* <span data-ttu-id="f2507-173">Cree una carpeta *Models* en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-173">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="f2507-174">Cree *Models/Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-174">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="f2507-175">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos que se corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="f2507-175">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="f2507-176">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-176">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f2507-177">Por tanto, el nombre que se reconoce de forma automática para la clave principal de la clase `Student` es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-177">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="f2507-178">Para más información, consulte [EF Core: claves](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="f2507-178">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="f2507-179">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f2507-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f2507-180">Las propiedades de navegación contienen otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="f2507-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="f2507-181">En este caso, la propiedad `Enrollments` de una entidad `Student` contiene todas las entidades `Enrollment` que están relacionadas con esa instancia de Student.</span><span class="sxs-lookup"><span data-stu-id="f2507-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="f2507-182">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades Enrollment.</span><span class="sxs-lookup"><span data-stu-id="f2507-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="f2507-183">En la base de datos, una fila Enrollment se relaciona con una fila Student si su columna StudentID contiene el valor de identificador del alumno.</span><span class="sxs-lookup"><span data-stu-id="f2507-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="f2507-184">Por ejemplo, imagine que una fila Student tiene el identificador 1.</span><span class="sxs-lookup"><span data-stu-id="f2507-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="f2507-185">Las filas Enrollment relacionadas tendrán StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="f2507-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="f2507-186">StudentID es una *clave externa* de la tabla Enrollment.</span><span class="sxs-lookup"><span data-stu-id="f2507-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="f2507-187">La propiedad `Enrollments` se define como `ICollection<Enrollment>` porque puede haber varias entidades Enrollment relacionadas.</span><span class="sxs-lookup"><span data-stu-id="f2507-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="f2507-188">Puede usar otros tipos de colección, como `List<Enrollment>` o `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="f2507-189">Cuando se usa `ICollection<Enrollment>`, EF Core crea una colección `HashSet<Enrollment>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f2507-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="f2507-190">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="f2507-190">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="f2507-192">Cree *Models/Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="f2507-193">La propiedad `EnrollmentID` es la clave principal; esta entidad usa el patrón `classnameID` en lugar de `ID` por sí solo.</span><span class="sxs-lookup"><span data-stu-id="f2507-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="f2507-194">Para un modelo de datos de producción, elija un patrón y úselo de forma coherente.</span><span class="sxs-lookup"><span data-stu-id="f2507-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="f2507-195">En este tutorial se usan los dos simplemente para ilustrar el trabajo.</span><span class="sxs-lookup"><span data-stu-id="f2507-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="f2507-196">El uso de `ID` sin `classname` facilita la implementación de algunos tipos de cambios del modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="f2507-197">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="f2507-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f2507-198">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade`[acepta valores NULL](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="f2507-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="f2507-199">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="f2507-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f2507-200">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f2507-201">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="f2507-202">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="f2507-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f2507-203">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="f2507-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f2507-204">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f2507-205">Por ejemplo, `StudentID` es la clave externa para la propiedad de navegación `Student`, ya que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f2507-206">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f2507-207">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="f2507-208">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="f2507-208">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="f2507-210">Cree *Models/Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="f2507-211">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="f2507-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f2507-212">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f2507-213">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="f2507-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="f2507-214">Compile el proyecto para comprobar que no hay errores de compilación.</span><span class="sxs-lookup"><span data-stu-id="f2507-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="f2507-215">Scaffolding de las páginas Student</span><span class="sxs-lookup"><span data-stu-id="f2507-215">Scaffold Student pages</span></span>

<span data-ttu-id="f2507-216">En esta sección, se usa la herramienta de scaffolding de ASP.NET Core para generar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="f2507-217">Una clase de *contexto* de EF Core.</span><span class="sxs-lookup"><span data-stu-id="f2507-217">An EF Core *context* class.</span></span> <span data-ttu-id="f2507-218">El contexto es la clase principal que coordina la funcionalidad de Entity Framework para un modelo de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="f2507-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="f2507-219">Se deriva de la clase `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f2507-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="f2507-220">Páginas de Razor que controlan las operaciones de creación, lectura, actualización y eliminación (CRUD) de la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2507-222">Cree una carpeta *Students* en la carpeta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f2507-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="f2507-223">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* y seleccione **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="f2507-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f2507-224">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="f2507-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="f2507-225">En el cuadro de diálogo para **agregar páginas de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f2507-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="f2507-226">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="f2507-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="f2507-227">En la fila **Clase de contexto de datos**, seleccione el signo **+** (más).</span><span class="sxs-lookup"><span data-stu-id="f2507-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="f2507-228">Cambie el nombre del contexto de datos *ContosoUniversity.Models.ContosoUniversityContext* por *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="f2507-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="f2507-229">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="f2507-229">Select **Add**.</span></span>

<span data-ttu-id="f2507-230">Los paquetes siguientes se instalan de forma automática:</span><span class="sxs-lookup"><span data-stu-id="f2507-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2507-232">Ejecute los siguientes comandos de la CLI de .NET Core para instalar los paquetes NuGet necesarios:</span><span class="sxs-lookup"><span data-stu-id="f2507-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="f2507-233">El paquete Microsoft.VisualStudio.Web.CodeGeneration.Design es necesario para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="f2507-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="f2507-234">Aunque en la aplicación no se usará SQL Server, la herramienta de scaffolding necesita el paquete de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f2507-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="f2507-235">Cree una carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f2507-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="f2507-236">Ejecute el comando siguiente para instalar la [herramienta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="f2507-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="f2507-237">Ejecute el comando siguiente para aplicar scaffolding a las páginas Student.</span><span class="sxs-lookup"><span data-stu-id="f2507-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="f2507-238">**En Windows**</span><span class="sxs-lookup"><span data-stu-id="f2507-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="f2507-239">**En macOS o Linux**</span><span class="sxs-lookup"><span data-stu-id="f2507-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="f2507-240">Si tiene un problema con el paso anterior, compile el proyecto y vuelva a intentar el paso de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="f2507-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="f2507-241">El proceso de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="f2507-241">The scaffolding process:</span></span>

* <span data-ttu-id="f2507-242">Crea páginas de Razor en la carpeta *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="f2507-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="f2507-243">*Create.cshtml* y *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f2507-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="f2507-244">*Delete.cshtml* y *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f2507-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="f2507-245">*Details.cshtml* y *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f2507-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="f2507-246">*Edit.cshtml* y *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f2507-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="f2507-247">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f2507-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="f2507-248">Crea *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="f2507-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="f2507-249">Agrega el contexto a la inserción de dependencias en *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f2507-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="f2507-250">Agrega una cadena de conexión de base de datos a *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f2507-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="f2507-251">Cadena de conexión de base de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2507-253">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="f2507-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="f2507-254">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="f2507-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f2507-255">De forma predeterminada, LocalDB crea archivos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2507-257">Cambie la cadena de conexión para que apunte a un archivo de base de datos de SQLite denominado *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="f2507-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="f2507-258">Actualización de la clase de contexto de base de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-258">Update the database context class</span></span>

<span data-ttu-id="f2507-259">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="f2507-260">El contexto se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f2507-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f2507-261">En el contexto se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f2507-262">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f2507-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f2507-263">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="f2507-264">El código resaltado crea una propiedad [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="f2507-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f2507-265">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="f2507-265">In EF Core terminology:</span></span>

* <span data-ttu-id="f2507-266">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="f2507-267">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="f2507-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f2507-268">Puesto que un conjunto de entidades contiene varias entidades, las propiedades DBSet deben ser nombres en plural.</span><span class="sxs-lookup"><span data-stu-id="f2507-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="f2507-269">Como la herramienta de scaffolding ha creado una instancia `Student` de DBSet, en este paso se cambia a `Students` en plural.</span><span class="sxs-lookup"><span data-stu-id="f2507-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="f2507-270">Para que el código de Razor Pages coincida con el nuevo nombre de DBSet, realice un cambio global de `_context.Student` a `_context.Students` en todo el proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="f2507-271">Hay ocho repeticiones.</span><span class="sxs-lookup"><span data-stu-id="f2507-271">There are 8 occurrences.</span></span>

<span data-ttu-id="f2507-272">Compile el proyecto para comprobar que no haya errores del compilador.</span><span class="sxs-lookup"><span data-stu-id="f2507-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="f2507-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="f2507-273">Startup.cs</span></span>

<span data-ttu-id="f2507-274">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f2507-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f2507-275">Los servicios (como el contexto de base de datos de EF Core) se registran con la inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f2507-276">Estos servicios se proporcionan a los componentes que los necesitan (como las páginas de Razor) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="f2507-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f2507-277">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="f2507-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f2507-278">La herramienta de scaffolding ha registrado de forma automática la clase de contexto con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f2507-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2507-280">En `ConfigureServices`, el proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="f2507-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2507-282">En `ConfigureServices`, asegúrese de que el código agregado por el proveedor de scaffolding llama a `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="f2507-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="f2507-283">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="f2507-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f2507-284">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f2507-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f2507-285">Creación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-285">Create the database</span></span>

<span data-ttu-id="f2507-286">Actualice *Program.cs* para crear la base de datos si no existe:</span><span class="sxs-lookup"><span data-stu-id="f2507-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="f2507-287">El método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) no realiza ninguna acción si existe una base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="f2507-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="f2507-288">Si no existe ninguna base de datos, se crean la base de datos y el esquema.</span><span class="sxs-lookup"><span data-stu-id="f2507-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="f2507-289">`EnsureCreated` habilita el flujo de trabajo siguiente para controlar los cambios del modelo de datos:</span><span class="sxs-lookup"><span data-stu-id="f2507-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="f2507-290">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-290">Delete the database.</span></span> <span data-ttu-id="f2507-291">Se pierden los datos existentes.</span><span class="sxs-lookup"><span data-stu-id="f2507-291">Any existing data is lost.</span></span>
* <span data-ttu-id="f2507-292">Se cambia el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-292">Change the data model.</span></span> <span data-ttu-id="f2507-293">Por ejemplo, se agrega un campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="f2507-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="f2507-294">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-294">Run the app.</span></span>
* <span data-ttu-id="f2507-295">`EnsureCreated` crea una base de datos con el esquema nuevo.</span><span class="sxs-lookup"><span data-stu-id="f2507-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="f2507-296">Este flujo de trabajo funciona bien al principio de la fase de desarrollo cuando el esquema evoluciona rápidamente, siempre y cuando no sea necesario conservar los datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="f2507-297">La situación es distinta cuando es necesario conservar los datos introducidos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="f2507-298">En ese caso, use las migraciones.</span><span class="sxs-lookup"><span data-stu-id="f2507-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="f2507-299">Más adelante en la serie de tutoriales, eliminará la base de datos creada por `EnsureCreated` y, en su lugar, usará las migraciones.</span><span class="sxs-lookup"><span data-stu-id="f2507-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="f2507-300">Una base de datos creada por `EnsureCreated` no se puede actualizar mediante migraciones.</span><span class="sxs-lookup"><span data-stu-id="f2507-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f2507-301">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f2507-301">Test the app</span></span>

* <span data-ttu-id="f2507-302">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-302">Run the app.</span></span>
* <span data-ttu-id="f2507-303">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="f2507-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f2507-304">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="f2507-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="f2507-305">Inicializar la base de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-305">Seed the database</span></span>

<span data-ttu-id="f2507-306">El método `EnsureCreated` crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="f2507-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="f2507-307">En esta sección se agrega código que rellena la base de datos con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="f2507-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="f2507-308">Cree *Data/DbInitializer.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-308">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="f2507-309">El código comprueba si hay alumnos en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="f2507-310">Si no hay ningún alumno, agrega datos de prueba a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="f2507-311">Crea los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="f2507-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="f2507-312">En *Program.cs*, reemplace la llamada a `EnsureCreated` con una llamada a `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="f2507-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2507-314">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="f2507-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2507-316">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f2507-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="f2507-317">Reinicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-317">Restart the app.</span></span>

* <span data-ttu-id="f2507-318">Seleccione la página Students para ver los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="f2507-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="f2507-319">Consulta la base de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2507-321">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f2507-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="f2507-322">En SSOX, seleccione **(localdb)\MSSQLLocalDB > Bases de datos > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="f2507-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="f2507-323">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="f2507-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="f2507-324">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="f2507-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="f2507-325">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="f2507-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="f2507-326">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver código** para ver cómo el modelo `Student` se asigna al esquema de tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2507-328">Use la herramienta SQLite para ver el esquema de la base de datos y los datos inicializados.</span><span class="sxs-lookup"><span data-stu-id="f2507-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="f2507-329">El archivo de base de datos se denomina *CU.db* y se encuentra en la carpeta del proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="f2507-330">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="f2507-330">Asynchronous code</span></span>

<span data-ttu-id="f2507-331">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="f2507-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f2507-332">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="f2507-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f2507-333">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="f2507-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f2507-334">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="f2507-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f2507-335">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f2507-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f2507-336">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz y el servidor pueda administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="f2507-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="f2507-337">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="f2507-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f2507-338">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="f2507-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f2507-339">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="f2507-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="f2507-340">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="f2507-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f2507-341">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="f2507-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f2507-342">Cree el objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="f2507-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="f2507-343">El tipo devuelto `Task<T>` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="f2507-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="f2507-344">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="f2507-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f2507-345">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="f2507-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f2507-346">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="f2507-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f2507-347">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="f2507-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f2507-348">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="f2507-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f2507-349">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="f2507-350">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2507-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f2507-351">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="f2507-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f2507-352">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="f2507-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f2507-353">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para la paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="f2507-354">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f2507-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f2507-355">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="f2507-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f2507-356">Siguiente tutorial</span><span class="sxs-lookup"><span data-stu-id="f2507-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f2507-357">En la aplicación web de ejemplo Contoso University se muestra cómo crear una aplicación web de Razor Pages de ASP.NET Core con Entity Framework (EF) Core.</span><span class="sxs-lookup"><span data-stu-id="f2507-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="f2507-358">La aplicación de ejemplo es un sitio web de una universidad ficticia, Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f2507-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f2507-359">Incluye funciones como la admisión de estudiantes, la creación de cursos y asignaciones de instructores.</span><span class="sxs-lookup"><span data-stu-id="f2507-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f2507-360">Esta página es la primera de una serie de tutoriales en los que se explica cómo crear la aplicación de ejemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f2507-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="f2507-361">Descargue o vea la aplicación completa.</span><span class="sxs-lookup"><span data-stu-id="f2507-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f2507-362">[Instrucciones de descarga](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f2507-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f2507-363">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="f2507-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="f2507-366">Familiaridad con las [Páginas de Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="f2507-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="f2507-367">Los programadores nuevos deben completar [Introducción a las páginas de Razor en ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start) antes de empezar esta serie.</span><span class="sxs-lookup"><span data-stu-id="f2507-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f2507-368">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="f2507-368">Troubleshooting</span></span>

<span data-ttu-id="f2507-369">Si experimenta un problema que no puede resolver, por lo general podrá encontrar la solución si compara el código con el [proyecto completado](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f2507-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f2507-370">Una buena forma de obtener ayuda consiste en publicar una pregunta en [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) para [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="f2507-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="f2507-371">La aplicación web Contoso University</span><span class="sxs-lookup"><span data-stu-id="f2507-371">The Contoso University web app</span></span>

<span data-ttu-id="f2507-372">La aplicación compilada en estos tutoriales es un sitio web básico de una universidad.</span><span class="sxs-lookup"><span data-stu-id="f2507-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="f2507-373">Los usuarios pueden ver y actualizar la información de estudiantes, cursos e instructores.</span><span class="sxs-lookup"><span data-stu-id="f2507-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f2507-374">Estas son algunas de las pantallas que se crean en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="f2507-374">Here are a few of the screens created in the tutorial.</span></span>

![Página de índice de Students](intro/_static/students-index.png)

![Página de edición de estudiantes](intro/_static/student-edit.png)

<span data-ttu-id="f2507-377">El estilo de la interfaz de usuario de este sitio se mantiene fiel a lo que generan las plantillas integradas.</span><span class="sxs-lookup"><span data-stu-id="f2507-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="f2507-378">El tutorial se centra en EF Core con páginas de Razor, no en la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="f2507-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="f2507-379">Creación de la aplicación web de Razor Pages ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="f2507-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2507-381">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="f2507-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f2507-382">Cree una aplicación web de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f2507-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="f2507-383">Asigne el nombre **ContosoUniversity** al proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="f2507-384">Es importante que el nombre del proyecto sea *ContosoUniversity* para que coincidan los espacios de nombres al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="f2507-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="f2507-385">Seleccione **ASP.NET Core 2.1** en la lista desplegable y, luego, **Aplicación web**.</span><span class="sxs-lookup"><span data-stu-id="f2507-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="f2507-386">Para ver las imágenes de los pasos anteriores, consulte [Creación de una aplicación web de Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="f2507-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="f2507-387">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f2507-389">Configurar el estilo del sitio</span><span class="sxs-lookup"><span data-stu-id="f2507-389">Set up the site style</span></span>

<span data-ttu-id="f2507-390">Con algunos cambios se configura el menú del sitio, el diseño y la página principal.</span><span class="sxs-lookup"><span data-stu-id="f2507-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="f2507-391">Actualice *Pages/Shared/_Layout.cshtml* con los cambios siguientes:</span><span class="sxs-lookup"><span data-stu-id="f2507-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="f2507-392">Cambie todas las repeticiones de "ContosoUniversity" por "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="f2507-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f2507-393">Hay tres repeticiones.</span><span class="sxs-lookup"><span data-stu-id="f2507-393">There are three occurrences.</span></span>

* <span data-ttu-id="f2507-394">Agregue entradas de menú para **Students**, **Courses**, **Instructors** y **Departments**, y elimine la entrada de menú **Contact**.</span><span class="sxs-lookup"><span data-stu-id="f2507-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="f2507-395">Los cambios aparecen resaltados.</span><span class="sxs-lookup"><span data-stu-id="f2507-395">The changes are highlighted.</span></span> <span data-ttu-id="f2507-396">(*No* se muestra todo el marcado).</span><span class="sxs-lookup"><span data-stu-id="f2507-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="f2507-397">En *Pages/Index.cshtml*, reemplace el contenido del archivo con el código siguiente para reemplazar el texto sobre ASP.NET y MVC con texto sobre esta aplicación:</span><span class="sxs-lookup"><span data-stu-id="f2507-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="f2507-398">Crear el modelo de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-398">Create the data model</span></span>

<span data-ttu-id="f2507-399">Cree las clases de entidad para la aplicación Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f2507-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="f2507-400">Comience con las tres entidades siguientes:</span><span class="sxs-lookup"><span data-stu-id="f2507-400">Start with the following three entities:</span></span>

![Diagrama del modelo de datos Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="f2507-402">Hay una relación uno a varios entre las entidades `Student` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="f2507-403">Hay una relación uno a varios entre las entidades `Course` y `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="f2507-404">Un estudiante se puede inscribir en cualquier número de cursos.</span><span class="sxs-lookup"><span data-stu-id="f2507-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="f2507-405">Un curso puede tener cualquier número de alumnos inscritos.</span><span class="sxs-lookup"><span data-stu-id="f2507-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="f2507-406">En las secciones siguientes, se crea una clase para cada una de estas entidades.</span><span class="sxs-lookup"><span data-stu-id="f2507-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="f2507-407">La entidad Student</span><span class="sxs-lookup"><span data-stu-id="f2507-407">The Student entity</span></span>

![Diagrama de la entidad Student](intro/_static/student-entity.png)

<span data-ttu-id="f2507-409">Cree una carpeta *Models*.</span><span class="sxs-lookup"><span data-stu-id="f2507-409">Create a *Models* folder.</span></span> <span data-ttu-id="f2507-410">En la carpeta *Models*, cree un archivo de clase denominado *Student.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="f2507-411">La propiedad `ID` se convierte en la columna de clave principal de la tabla de base de datos (DB) que corresponde a esta clase.</span><span class="sxs-lookup"><span data-stu-id="f2507-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="f2507-412">De forma predeterminada, EF Core interpreta como la clave principal una propiedad que se denomine `ID` o `classnameID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f2507-413">En `classnameID`, `classname` es el nombre de la clase.</span><span class="sxs-lookup"><span data-stu-id="f2507-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="f2507-414">En el ejemplo anterior, la clave principal alternativa que se reconoce de forma automática es `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="f2507-415">La propiedad `Enrollments` es una [propiedad de navegación](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f2507-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f2507-416">Las propiedades de navegación se vinculan a otras entidades relacionadas con esta entidad.</span><span class="sxs-lookup"><span data-stu-id="f2507-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="f2507-417">En este caso, la propiedad `Enrollments` de una `Student entity` contiene todas las entidades `Enrollment` que están relacionadas con esa entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="f2507-418">Por ejemplo, si una fila Student de la base de datos tiene dos filas Enrollment relacionadas, la propiedad de navegación `Enrollments` contiene esas dos entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="f2507-419">Una fila `Enrollment` relacionada es la que contiene el valor de clave principal de ese estudiante en la columna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="f2507-420">Por ejemplo, suponga que el estudiante con ID=1 tiene dos filas en la tabla `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="f2507-421">La tabla `Enrollment` tiene dos filas con `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="f2507-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="f2507-422">`StudentID` es una clave externa en la tabla `Enrollment` que especifica el estudiante en la tabla `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="f2507-423">Si una propiedad de navegación puede contener varias entidades, la propiedad de navegación debe ser un tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="f2507-424">Se puede especificar `ICollection<T>`, o bien un tipo como `List<T>` o `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="f2507-425">Cuando se usa `ICollection<T>`, EF Core crea una colección `HashSet<T>` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f2507-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="f2507-426">Las propiedades de navegación que contienen varias entidades proceden de relaciones de varios a varios y uno a varios.</span><span class="sxs-lookup"><span data-stu-id="f2507-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="f2507-427">La entidad Enrollment</span><span class="sxs-lookup"><span data-stu-id="f2507-427">The Enrollment entity</span></span>

![Diagrama de la entidad Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="f2507-429">En la carpeta *Models*, cree *Enrollment.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="f2507-430">La propiedad `EnrollmentID` es la clave principal.</span><span class="sxs-lookup"><span data-stu-id="f2507-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="f2507-431">En esta entidad se usa el patrón `classnameID` en lugar de `ID` como en la entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="f2507-432">Normalmente, los desarrolladores eligen un patrón y lo usan en todo el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="f2507-433">En un tutorial posterior, se muestra el uso de ID sin un nombre de clase para facilitar la implementación de la herencia en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="f2507-434">La propiedad `Grade` es una `enum`.</span><span class="sxs-lookup"><span data-stu-id="f2507-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f2507-435">El signo de interrogación después de la declaración de tipo `Grade` indica que la propiedad `Grade` acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="f2507-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="f2507-436">Una calificación que sea NULL es diferente de una calificación que sea cero; NULL significa que no se conoce una calificación o que todavía no se ha asignado.</span><span class="sxs-lookup"><span data-stu-id="f2507-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f2507-437">La propiedad `StudentID` es una clave externa y la propiedad de navegación correspondiente es `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f2507-438">Una entidad `Enrollment` está asociada con una entidad `Student`, por lo que la propiedad contiene una única entidad `Student`.</span><span class="sxs-lookup"><span data-stu-id="f2507-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="f2507-439">La entidad `Student` difiere de la propiedad de navegación `Student.Enrollments`, que contiene varias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="f2507-440">La propiedad `CourseID` es una clave externa y la propiedad de navegación correspondiente es `Course`.</span><span class="sxs-lookup"><span data-stu-id="f2507-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f2507-441">Una entidad `Enrollment` está asociada con una entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="f2507-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f2507-442">EF Core interpreta una propiedad como una clave externa si se denomina `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f2507-443">Por ejemplo, `StudentID` para la propiedad de navegación `Student`, puesto que la clave principal de la entidad `Student` es `ID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f2507-444">Las propiedades de clave externa también se pueden denominar `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f2507-445">Por ejemplo `CourseID`, dado que la clave principal de la entidad `Course` es `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="f2507-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="f2507-446">La entidad Course</span><span class="sxs-lookup"><span data-stu-id="f2507-446">The Course entity</span></span>

![Diagrama de la entidad Course](intro/_static/course-entity.png)

<span data-ttu-id="f2507-448">En la carpeta *Models*, cree *Course.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="f2507-449">La propiedad `Enrollments` es una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="f2507-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f2507-450">Una entidad `Course` puede estar relacionada con cualquier número de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f2507-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f2507-451">El atributo `DatabaseGenerated` permite que la aplicación especifique la clave principal en lugar de hacer que la base de datos la genere.</span><span class="sxs-lookup"><span data-stu-id="f2507-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="f2507-452">Aplicación de scaffolding al modelo de alumnos</span><span class="sxs-lookup"><span data-stu-id="f2507-452">Scaffold the student model</span></span>

<span data-ttu-id="f2507-453">En esta sección, se aplica scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="f2507-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="f2507-454">Es decir, la herramienta de scaffolding genera páginas para las operaciones de creación, lectura, actualización y eliminación (CRUD) del modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="f2507-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="f2507-455">Compile el proyecto.</span><span class="sxs-lookup"><span data-stu-id="f2507-455">Build the project.</span></span>
* <span data-ttu-id="f2507-456">Cree la carpeta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f2507-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f2507-458">En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta *Pages/Students* > **Agregar** > **Nuevo elemento con scaffold**.</span><span class="sxs-lookup"><span data-stu-id="f2507-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f2507-459">En el cuadro de diálogo **Agregar scaffold**, seleccione **Páginas de Razor que usan Entity Framework (CRUD)** > **AGREGAR**.</span><span class="sxs-lookup"><span data-stu-id="f2507-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="f2507-460">Complete el cuadro de diálogo para **agregar páginas de Razor Pages que usan Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f2507-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f2507-461">En la lista desplegable **Clase de modelo**, seleccione **Student (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="f2507-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="f2507-462">En la fila **Clase de contexto de datos**, haga clic en el signo **+** (más) y cambie el nombre generado por **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="f2507-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="f2507-463">En la lista desplegable **Clase de contexto de datos**, seleccione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="f2507-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="f2507-464">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="f2507-464">Select **Add**.</span></span>

![Cuadro de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="f2507-466">Si tiene algún problema con el paso anterior, consulte [Aplicar scaffolding al modelo de película](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="f2507-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f2507-468">Ejecute los comandos siguientes para aplicar scaffolding al modelo de alumnos.</span><span class="sxs-lookup"><span data-stu-id="f2507-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="f2507-469">El proceso de scaffolding ha creado y cambiado los archivos siguientes:</span><span class="sxs-lookup"><span data-stu-id="f2507-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="f2507-470">Archivos creados</span><span class="sxs-lookup"><span data-stu-id="f2507-470">Files created</span></span>

* <span data-ttu-id="f2507-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span><span class="sxs-lookup"><span data-stu-id="f2507-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="f2507-472">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f2507-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="f2507-473">Actualizaciones de archivos</span><span class="sxs-lookup"><span data-stu-id="f2507-473">File updates</span></span>

* <span data-ttu-id="f2507-474">*Startup.cs*: en la sección siguiente se detallan los cambios realizados en este archivo.</span><span class="sxs-lookup"><span data-stu-id="f2507-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="f2507-475">*appsettings.json*: se agrega la cadena de conexión que se usa para conectarse a una base de datos local.</span><span class="sxs-lookup"><span data-stu-id="f2507-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f2507-476">Examinar el contexto registrado con la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="f2507-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f2507-477">ASP.NET Core integra la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f2507-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f2507-478">Los servicios (como el contexto de base de datos de EF Core) se registran con inserción de dependencias durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f2507-479">Estos servicios se proporcionan a los componentes que los necesitan (como las páginas de Razor) a través de parámetros de constructor.</span><span class="sxs-lookup"><span data-stu-id="f2507-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f2507-480">El código de constructor que obtiene una instancia de contexto de base de datos se muestra más adelante en el tutorial.</span><span class="sxs-lookup"><span data-stu-id="f2507-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f2507-481">La herramienta de scaffolding creó de forma automática un contexto de base de datos y lo registró con el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f2507-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f2507-482">Examine el método `ConfigureServices` de *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f2507-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="f2507-483">El proveedor de scaffolding ha agregado la línea resaltada:</span><span class="sxs-lookup"><span data-stu-id="f2507-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="f2507-484">El nombre de la cadena de conexión se pasa al contexto mediante una llamada a un método en un objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="f2507-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f2507-485">Para el desarrollo local, el [sistema de configuración de ASP.NET Core](xref:fundamentals/configuration/index) lee la cadena de conexión desde el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f2507-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="f2507-486">Actualización de main</span><span class="sxs-lookup"><span data-stu-id="f2507-486">Update main</span></span>

<span data-ttu-id="f2507-487">En *Program.cs*, modifique el método `Main` para que haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f2507-488">Obtener una instancia del contexto de base de datos desde el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="f2507-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f2507-489">Llame a [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="f2507-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="f2507-490">Elimine el contexto cuando finalice el método `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="f2507-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="f2507-491">En el código siguiente se muestra el archivo *Program.cs* actualizado.</span><span class="sxs-lookup"><span data-stu-id="f2507-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="f2507-492">`EnsureCreated` garantiza la existencia de la base de datos para el contexto.</span><span class="sxs-lookup"><span data-stu-id="f2507-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="f2507-493">Si existe, no se realiza ninguna acción.</span><span class="sxs-lookup"><span data-stu-id="f2507-493">If it exists, no action is taken.</span></span> <span data-ttu-id="f2507-494">Si no existe, se crean la base de datos y todo su esquema.</span><span class="sxs-lookup"><span data-stu-id="f2507-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="f2507-495">En `EnsureCreated` no se usan migraciones para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="f2507-496">Una base de datos que se cree con `EnsureCreated` no se podrá actualizar más adelante mediante las migraciones.</span><span class="sxs-lookup"><span data-stu-id="f2507-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="f2507-497">`EnsureCreated` se llama durante el inicio de la aplicación, lo que permite el flujo de trabajo siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="f2507-498">Se elimina la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-498">Delete the DB.</span></span>
* <span data-ttu-id="f2507-499">Se cambia el esquema de base de datos (por ejemplo, se agrega un campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="f2507-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="f2507-500">Ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f2507-500">Run the app.</span></span>
* <span data-ttu-id="f2507-501">`EnsureCreated` crea una base de datos con la columna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="f2507-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="f2507-502">`EnsureCreated` es útil al principio del desarrollo, cuando el esquema evoluciona rápidamente.</span><span class="sxs-lookup"><span data-stu-id="f2507-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="f2507-503">Más adelante, en el tutorial se elimina la base de datos y se usan las migraciones.</span><span class="sxs-lookup"><span data-stu-id="f2507-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f2507-504">Prueba de la aplicación</span><span class="sxs-lookup"><span data-stu-id="f2507-504">Test the app</span></span>

<span data-ttu-id="f2507-505">Ejecute la aplicación y acepte la directiva de cookies.</span><span class="sxs-lookup"><span data-stu-id="f2507-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="f2507-506">Esta aplicación no conserva información de carácter personal.</span><span class="sxs-lookup"><span data-stu-id="f2507-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="f2507-507">Puede obtener más información sobre la directiva de cookies en [Compatibilidad con el Reglamento general de protección de datos (RGPD) de la UE](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f2507-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="f2507-508">Haga clic en el vínculo **Students** y, después, en **Crear nuevo**.</span><span class="sxs-lookup"><span data-stu-id="f2507-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f2507-509">Pruebe los vínculos Edit, Details y Delete.</span><span class="sxs-lookup"><span data-stu-id="f2507-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="f2507-510">Examinar el contexto de base de datos SchoolContext</span><span class="sxs-lookup"><span data-stu-id="f2507-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="f2507-511">La clase principal que coordina la funcionalidad de EF Core para un modelo de datos determinado es la clase de contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="f2507-512">El contexto de datos se deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f2507-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f2507-513">En el contexto de datos se especifica qué entidades se incluyen en el modelo de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f2507-514">En este proyecto, la clase se denomina `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f2507-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f2507-515">Actualice *SchoolContext.cs* con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="f2507-516">El código resaltado crea una propiedad [DbSet\<TEntity >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="f2507-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f2507-517">En la terminología de EF Core:</span><span class="sxs-lookup"><span data-stu-id="f2507-517">In EF Core terminology:</span></span>

* <span data-ttu-id="f2507-518">Un conjunto de entidades normalmente se corresponde a una tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="f2507-519">Una entidad se corresponde con una fila de la tabla.</span><span class="sxs-lookup"><span data-stu-id="f2507-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f2507-520">`DbSet<Enrollment>` y `DbSet<Course>` se pueden omitir.</span><span class="sxs-lookup"><span data-stu-id="f2507-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="f2507-521">EF Core las incluye implícitamente porque la entidad `Student` hace referencia a la entidad `Enrollment` y la entidad `Enrollment` hace referencia a la entidad `Course`.</span><span class="sxs-lookup"><span data-stu-id="f2507-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="f2507-522">Para este tutorial, conserve `DbSet<Enrollment>` y `DbSet<Course>` en el `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f2507-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="f2507-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f2507-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f2507-524">La cadena de conexión especifica [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="f2507-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="f2507-525">LocalDB es una versión ligera del motor de base de datos de SQL Server Express y está dirigida al desarrollo de aplicaciones, no al uso en producción.</span><span class="sxs-lookup"><span data-stu-id="f2507-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f2507-526">LocalDB se inicia a petición y se ejecuta en modo de usuario, sin necesidad de una configuración compleja.</span><span class="sxs-lookup"><span data-stu-id="f2507-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f2507-527">De forma predeterminada, LocalDB crea archivos de base de datos *.mdf* en el directorio `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="f2507-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="f2507-528">Agregar código para inicializar la base de datos con datos de prueba</span><span class="sxs-lookup"><span data-stu-id="f2507-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="f2507-529">EF Core crea una base de datos vacía.</span><span class="sxs-lookup"><span data-stu-id="f2507-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="f2507-530">En esta sección, se escribe un método `Initialize` para rellenarlo con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="f2507-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="f2507-531">En la carpeta *Data*, cree un archivo de clase denominado *DbInitializer.cs* y agregue el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="f2507-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="f2507-532">Nota: El código anterior usa `Models` para el espacio de nombres (`namespace ContosoUniversity.Models`) en lugar de `Data`.</span><span class="sxs-lookup"><span data-stu-id="f2507-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="f2507-533">`Models` es coherente con el código generado por el proveedor de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="f2507-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="f2507-534">Para obtener más información, consulte [este problema de scaffolding de GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="f2507-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="f2507-535">El código comprueba si hay estudiantes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="f2507-536">Si no hay alumnos en la base de datos, se inicializa con datos de prueba.</span><span class="sxs-lookup"><span data-stu-id="f2507-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="f2507-537">Carga los datos de prueba en matrices en lugar de colecciones `List<T>` para optimizar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="f2507-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="f2507-538">El método `EnsureCreated` crea automáticamente la base de datos para el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="f2507-539">Si la base de datos existe, `EnsureCreated` vuelve sin modificarla.</span><span class="sxs-lookup"><span data-stu-id="f2507-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="f2507-540">En *Program.cs*, modifique el método `Main` para que llame a `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="f2507-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="f2507-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f2507-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f2507-542">Detenga la aplciación si se está ejecutando y ejecute el comando siguiente en la **Consola del Administrador de paquetes** (PMC):</span><span class="sxs-lookup"><span data-stu-id="f2507-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f2507-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f2507-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f2507-544">Detenga la aplicación si se está ejecutando y elimine el archivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f2507-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="f2507-545">Ver la base de datos</span><span class="sxs-lookup"><span data-stu-id="f2507-545">View the DB</span></span>

<span data-ttu-id="f2507-546">El nombre de la base de datos se genera a partir del nombre de contexto proporcionado anteriormente, más un guión y un GUID.</span><span class="sxs-lookup"><span data-stu-id="f2507-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="f2507-547">Por lo tanto, el nombre de la base de datos será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="f2507-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="f2507-548">El GUID será diferente para cada usuario.</span><span class="sxs-lookup"><span data-stu-id="f2507-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="f2507-549">Abra el **Explorador de objetos de SQL Server** (SSOX) desde el menú **Vista** en Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f2507-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="f2507-550">En SSOX, haga clic en **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="f2507-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="f2507-551">Expanda el nodo **Tablas**.</span><span class="sxs-lookup"><span data-stu-id="f2507-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="f2507-552">Haga clic con el botón derecho en la tabla **Student** y haga clic en **Ver datos** para ver las columnas que se crearon y las filas que se insertaron en la tabla.</span><span class="sxs-lookup"><span data-stu-id="f2507-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="f2507-553">Código asincrónico</span><span class="sxs-lookup"><span data-stu-id="f2507-553">Asynchronous code</span></span>

<span data-ttu-id="f2507-554">La programación asincrónica es el modo predeterminado de ASP.NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="f2507-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f2507-555">Un servidor web tiene un número limitado de subprocesos disponibles y, en situaciones de carga alta, es posible que todos los subprocesos disponibles estén en uso.</span><span class="sxs-lookup"><span data-stu-id="f2507-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f2507-556">Cuando esto ocurre, el servidor no puede procesar nuevas solicitudes hasta que los subprocesos se liberen.</span><span class="sxs-lookup"><span data-stu-id="f2507-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f2507-557">Con el código sincrónico, se pueden acumular muchos subprocesos mientras no estén realizando ningún trabajo porque están a la espera de que finalice la E/S.</span><span class="sxs-lookup"><span data-stu-id="f2507-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f2507-558">Con el código asincrónico, cuando un proceso está a la espera de que finalice la E/S, se libera su subproceso para el que el servidor lo use para el procesamiento de otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="f2507-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f2507-559">Como resultado, el código asincrónico permite que los recursos de servidor se usen de forma más eficaz, y el servidor está habilitado para administrar más tráfico sin retrasos.</span><span class="sxs-lookup"><span data-stu-id="f2507-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="f2507-560">El código asincrónico introduce una pequeña cantidad de sobrecarga en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="f2507-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f2507-561">En situaciones de poco tráfico, la disminución del rendimiento es insignificante, mientras que en situaciones de tráfico elevado, la posible mejora del rendimiento es importante.</span><span class="sxs-lookup"><span data-stu-id="f2507-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f2507-562">En el código siguiente, la palabra clave [async](/dotnet/csharp/language-reference/keywords/async), el valor devuelto `Task<T>`, la palabra clave `await` y el método `ToListAsync` hacen que el código se ejecute de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="f2507-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="f2507-563">La palabra clave `async` indica al compilador que:</span><span class="sxs-lookup"><span data-stu-id="f2507-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f2507-564">Genere devoluciones de llamada para partes del cuerpo del método.</span><span class="sxs-lookup"><span data-stu-id="f2507-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f2507-565">Cree automáticamente el objeto [Task](/dotnet/api/system.threading.tasks.task) que se devuelve.</span><span class="sxs-lookup"><span data-stu-id="f2507-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="f2507-566">Para más información, vea [Tipo de valor devuelto Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="f2507-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="f2507-567">El tipo devuelto implícito `Task` representa el trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="f2507-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="f2507-568">La palabra clave `await` hace que el compilador divida el método en dos partes.</span><span class="sxs-lookup"><span data-stu-id="f2507-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f2507-569">La primera parte termina con la operación que se inició de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="f2507-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f2507-570">La segunda parte se coloca en un método de devolución de llamada que se llama cuando finaliza la operación.</span><span class="sxs-lookup"><span data-stu-id="f2507-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f2507-571">`ToListAsync` es la versión asincrónica del método de extensión `ToList`.</span><span class="sxs-lookup"><span data-stu-id="f2507-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f2507-572">Algunos aspectos que tener en cuenta al escribir código asincrónico en el que se usa EF Core son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="f2507-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f2507-573">Solo se ejecutan de forma asincrónica las instrucciones que hacen que las consultas o los comandos se envíen a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="f2507-574">Esto incluye `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` y `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2507-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f2507-575">No incluye las instrucciones que solo cambian una `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="f2507-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f2507-576">Un contexto de EF Core no es seguro para subprocesos: no intente realizar varias operaciones en paralelo.</span><span class="sxs-lookup"><span data-stu-id="f2507-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f2507-577">Para aprovechar las ventajas de rendimiento del código asincrónico, compruebe que en los paquetes de biblioteca (por ejemplo para paginación) se usa async si llaman a métodos de EF Core que envían consultas a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f2507-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="f2507-578">Para obtener más información sobre la programación asincrónica en .NET, vea [Programación asincrónica](/dotnet/standard/async) y [Programación asincrónica con async y await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f2507-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="f2507-579">En el siguiente tutorial, se examinan las operaciones CRUD (crear, leer, actualizar y eliminar) básicas.</span><span class="sxs-lookup"><span data-stu-id="f2507-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="f2507-580">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="f2507-580">Additional resources</span></span>

* [<span data-ttu-id="f2507-581">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="f2507-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="f2507-582">Siguiente</span><span class="sxs-lookup"><span data-stu-id="f2507-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
