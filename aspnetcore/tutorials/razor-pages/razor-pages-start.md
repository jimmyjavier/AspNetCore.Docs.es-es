---
title: 'Tutorial: Introducción a Razor Pages en ASP.NET Core'
author: rick-anderson
description: En esta serie de tutoriales se muestra cómo usar Razor Pages en ASP.NET Core. Aprenda a crear un modelo, generar código para Razor Pages, usar Entity Framework Core y SQL Server para el acceso a datos, agregar la funcionalidad de búsqueda, agregar validación de entrada y usar migraciones para actualizar el modelo.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 8ed12b1778673962fe0b174e005bd6d8a7f54168
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774878"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="e735e-104">Tutorial: Introducción a Razor Pages en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e735e-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="e735e-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e735e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="e735e-106">Este es el primer tutorial de una serie de varios que enseña los aspectos básicos de la compilación de una aplicación web de Razor Pages de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e735e-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="e735e-107">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="e735e-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="e735e-108">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="e735e-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e735e-109">Crear una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e735e-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="e735e-110">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e735e-110">Run the app.</span></span>
> * <span data-ttu-id="e735e-111">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="e735e-111">Examine the project files.</span></span>

<span data-ttu-id="e735e-112">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="e735e-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="e735e-114">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e735e-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e735e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e735e-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e735e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e735e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e735e-117">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e735e-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="e735e-118">Creación de una aplicación web de páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="e735e-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e735e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e735e-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e735e-120">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="e735e-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e735e-121">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e735e-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="e735e-122">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="e735e-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="e735e-123">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="e735e-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="e735e-124">Es importante asignarle el nombre *RazorPagesMovie* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="e735e-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="e735e-125">![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="e735e-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="e735e-126">Seleccione **ASP.NET Core 3.1** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e735e-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="e735e-128">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="e735e-128">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e735e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e735e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e735e-131">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e735e-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="e735e-132">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e735e-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="e735e-133">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e735e-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="e735e-134">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="e735e-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="e735e-135">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e735e-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="e735e-136">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo que pregunta **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="e735e-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="e735e-137">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e735e-137">Select **Yes**.</span></span>

  <span data-ttu-id="e735e-138">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e735e-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e735e-139">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e735e-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e735e-140">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="e735e-140">Select **File** > **New Solution**.</span></span>

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="e735e-142">Seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e735e-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="e735e-144">En el cuadro de diálogo **Configure your new Web Application** (Configurar la nueva aplicación web), establezca el **marco de destino** en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="e735e-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Selección de .NET Core 3.1 de macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="e735e-146">Asigne el nombre **RazorPagesMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e735e-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="e735e-148">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="e735e-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="e735e-149">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="e735e-149">Examine the project files</span></span>

<span data-ttu-id="e735e-150">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="e735e-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="e735e-151">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="e735e-151">Pages folder</span></span>

<span data-ttu-id="e735e-152">Contiene Razor Pages y los archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="e735e-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="e735e-153">Cada página de Razor se compone de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="e735e-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="e735e-154">Archivo *.cshtml* que contiene el marcado HTML con código C# que usa la sintaxis Razor.</span><span class="sxs-lookup"><span data-stu-id="e735e-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="e735e-155">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="e735e-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="e735e-156">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="e735e-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="e735e-157">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="e735e-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="e735e-158">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="e735e-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="e735e-159">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="e735e-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="e735e-160">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="e735e-160">wwwroot folder</span></span>

<span data-ttu-id="e735e-161">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="e735e-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="e735e-162">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="e735e-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="e735e-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="e735e-163">appSettings.json</span></span>

<span data-ttu-id="e735e-164">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="e735e-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="e735e-165">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e735e-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="e735e-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="e735e-166">Program.cs</span></span>

<span data-ttu-id="e735e-167">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="e735e-167">Contains the entry point for the program.</span></span> <span data-ttu-id="e735e-168">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="e735e-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="e735e-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="e735e-169">Startup.cs</span></span>

<span data-ttu-id="e735e-170">Contiene código que configura el comportamiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e735e-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="e735e-171">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e735e-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e735e-172">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="e735e-172">Next steps</span></span>

<span data-ttu-id="e735e-173">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="e735e-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e735e-174">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="e735e-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e735e-175">Este es el primer tutorial de una serie.</span><span class="sxs-lookup"><span data-stu-id="e735e-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="e735e-176">En la [serie](xref:tutorials/razor-pages/index) se enseñan los conceptos básicos de la compilación de una aplicación web de Razor Pages en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e735e-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="e735e-177">Al final de la serie, tendrá una aplicación que puede administrar una base de datos de películas.</span><span class="sxs-lookup"><span data-stu-id="e735e-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="e735e-178">En este tutorial ha:</span><span class="sxs-lookup"><span data-stu-id="e735e-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e735e-179">Crear una aplicación web de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e735e-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="e735e-180">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e735e-180">Run the app.</span></span>
> * <span data-ttu-id="e735e-181">Examinar los archivos de proyecto.</span><span class="sxs-lookup"><span data-stu-id="e735e-181">Examine the project files.</span></span>

<span data-ttu-id="e735e-182">Al final de este tutorial, tendrá una aplicación web de Razor Pages que compilará en los tutoriales posteriores.</span><span class="sxs-lookup"><span data-stu-id="e735e-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="e735e-184">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="e735e-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e735e-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e735e-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e735e-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e735e-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e735e-187">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e735e-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="e735e-188">Creación de una aplicación web de páginas de Razor</span><span class="sxs-lookup"><span data-stu-id="e735e-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e735e-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e735e-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e735e-190">En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.</span><span class="sxs-lookup"><span data-stu-id="e735e-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="e735e-191">Cree una nueva aplicación web de ASP.NET Core y seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e735e-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="e735e-193">Asigne al proyecto el nombre **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="e735e-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="e735e-194">Es importante asignarle el nombre *RazorPagesMovie* para que los espacios de nombres coincidan al copiar y pegar el código.</span><span class="sxs-lookup"><span data-stu-id="e735e-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/config.png)

* <span data-ttu-id="e735e-196">Seleccione **ASP.NET Core 2.2** en la lista desplegable, después **Aplicación web** y, por último, **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e735e-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![Nueva aplicación web de ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="e735e-198">Se crea el proyecto de inicio siguiente:</span><span class="sxs-lookup"><span data-stu-id="e735e-198">The following starter project is created:</span></span>

  ![Explorador de soluciones](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="e735e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e735e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e735e-201">Abra el [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="e735e-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="e735e-202">Cambie al directorio (`cd`) que contiene el proyecto.</span><span class="sxs-lookup"><span data-stu-id="e735e-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="e735e-203">Ejecute los comandos siguientes:</span><span class="sxs-lookup"><span data-stu-id="e735e-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="e735e-204">El comando `dotnet new` crea un proyecto de Razor Pages en la carpeta *RazorPagesMovie*.</span><span class="sxs-lookup"><span data-stu-id="e735e-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="e735e-205">El comando `code` abre la carpeta *RazorPagesMovie* en la instancia actual de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e735e-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="e735e-206">Cuando el icono de llama de OmniSharp de la barra de estado se ponga verde, aparecerá un cuadro de diálogo que pregunta **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?** (Faltan los activos necesarios para compilar y depurar en "RazorPagesMovie". ¿Desea agregarlos?).</span><span class="sxs-lookup"><span data-stu-id="e735e-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="e735e-207">Seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="e735e-207">Select **Yes**.</span></span>

  <span data-ttu-id="e735e-208">Un directorio *.vscode*, que contiene archivos *launch.json* y *tasks.json*, se agrega al directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="e735e-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e735e-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e735e-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e735e-210">Seleccione **Archivo** > **Nueva solución**.</span><span class="sxs-lookup"><span data-stu-id="e735e-210">Select **File** > **New Solution**.</span></span>

![macOS: Nueva solución](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="e735e-212">Seleccione **.NET Core** > **Aplicación** > **Aplicación web** > **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="e735e-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![Cuadro de diálogo de nuevo proyecto de macOS](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="e735e-214">En el cuadro de diálogo **Configure your new ASP.NET Core Web API** (Configurar la nueva API web de ASP.NET Core), establezca la **plataforma de destino** en **.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="e735e-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![Selección de .NET Core 3.0 de macOS](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="e735e-216">Asigne el nombre **RazorPagesMovie** al proyecto y, después, seleccione **Crear**.</span><span class="sxs-lookup"><span data-stu-id="e735e-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="e735e-218">Ejecutar la aplicación</span><span class="sxs-lookup"><span data-stu-id="e735e-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e735e-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e735e-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e735e-220">Presione Ctrl+F5 para ejecutarla sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="e735e-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="e735e-221">Visual Studio inicia [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) y ejecuta la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e735e-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="e735e-222">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="e735e-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="e735e-223">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="e735e-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="e735e-224">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="e735e-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="e735e-225">Cuando Visual Studio crea un proyecto web, se usa un puerto aleatorio para el servidor web.</span><span class="sxs-lookup"><span data-stu-id="e735e-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="e735e-226">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="e735e-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="e735e-227">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="e735e-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="e735e-229">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="e735e-229">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="e735e-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e735e-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="e735e-232">Presione **Ctrl+F5** para ejecutar sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="e735e-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="e735e-233">Visual Studio Code inicia [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplaza hasta `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e735e-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="e735e-234">En la barra de direcciones aparece `localhost:port#` (y no algo como `example.com`).</span><span class="sxs-lookup"><span data-stu-id="e735e-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="e735e-235">Esto es así porque `localhost` es el nombre de host estándar del equipo local.</span><span class="sxs-lookup"><span data-stu-id="e735e-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="e735e-236">Localhost solo sirve las solicitudes web del equipo local.</span><span class="sxs-lookup"><span data-stu-id="e735e-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="e735e-237">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="e735e-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="e735e-238">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="e735e-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="e735e-240">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="e735e-240">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e735e-242">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e735e-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="e735e-243">Presione **Cmd-Opt-F5** para realizar la ejecución sin el depurador.</span><span class="sxs-lookup"><span data-stu-id="e735e-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="e735e-244">Visual Studio iniciará [Kestrel](xref:fundamentals/servers/kestrel) y un explorador, y se desplazará a `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e735e-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="e735e-245">En la página principal de la aplicación, seleccione **Aceptar** para dar su consentimiento al seguimiento.</span><span class="sxs-lookup"><span data-stu-id="e735e-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="e735e-246">Esta aplicación no realiza un seguimiento de la información personal, pero la plantilla del proyecto incluye la función de consentimiento en caso de que sea necesaria para cumplir con el [Reglamento general de protección de datos (RGPD)](xref:security/gdpr) de la Unión Europea.</span><span class="sxs-lookup"><span data-stu-id="e735e-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="e735e-248">En la siguiente imagen se muestra la aplicación tras haber dado su consentimiento al seguimiento:</span><span class="sxs-lookup"><span data-stu-id="e735e-248">The following image shows the app after you give consent to tracking:</span></span>

  ![Página Inicio o Índice](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="e735e-250">Examen de los archivo del proyecto</span><span class="sxs-lookup"><span data-stu-id="e735e-250">Examine the project files</span></span>

<span data-ttu-id="e735e-251">He aquí un resumen de las principales carpetas y archivos del proyecto con los que va a trabajar en los próximos tutoriales.</span><span class="sxs-lookup"><span data-stu-id="e735e-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="e735e-252">Carpeta Pages</span><span class="sxs-lookup"><span data-stu-id="e735e-252">Pages folder</span></span>

<span data-ttu-id="e735e-253">Contiene páginas de Razor y archivos auxiliares.</span><span class="sxs-lookup"><span data-stu-id="e735e-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="e735e-254">Cada página de Razor consta de un par de archivos:</span><span class="sxs-lookup"><span data-stu-id="e735e-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="e735e-255">Un archivo *.cshtml* que contiene marcado HTML con código de C# que usa sintaxis de Razor.</span><span class="sxs-lookup"><span data-stu-id="e735e-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="e735e-256">Archivo *. cshtml.cs* que contiene C# código que controla los eventos de página.</span><span class="sxs-lookup"><span data-stu-id="e735e-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="e735e-257">Los archivos auxiliares tienen nombres que comienzan con un carácter de subrayado.</span><span class="sxs-lookup"><span data-stu-id="e735e-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="e735e-258">Por ejemplo, el archivo *_Layout.cshtml* configura los elementos de la interfaz de usuario comunes a todas las páginas.</span><span class="sxs-lookup"><span data-stu-id="e735e-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="e735e-259">Este archivo configura el menú de navegación de la parte superior de la página y el aviso de copyright de la parte inferior de la página.</span><span class="sxs-lookup"><span data-stu-id="e735e-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="e735e-260">Para obtener más información, vea <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="e735e-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="e735e-261">Carpeta wwwroot</span><span class="sxs-lookup"><span data-stu-id="e735e-261">wwwroot folder</span></span>

<span data-ttu-id="e735e-262">Contiene los archivos estáticos, como los archivos HTML, los archivos de JavaScript y los archivos CSS.</span><span class="sxs-lookup"><span data-stu-id="e735e-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="e735e-263">Para obtener más información, vea <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="e735e-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="e735e-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="e735e-264">appSettings.json</span></span>

<span data-ttu-id="e735e-265">Contiene los datos de configuración, como las cadenas de conexión.</span><span class="sxs-lookup"><span data-stu-id="e735e-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="e735e-266">Para obtener más información, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e735e-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="e735e-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="e735e-267">Program.cs</span></span>

<span data-ttu-id="e735e-268">Contiene el punto de entrada del programa.</span><span class="sxs-lookup"><span data-stu-id="e735e-268">Contains the entry point for the program.</span></span> <span data-ttu-id="e735e-269">Para obtener más información, vea <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="e735e-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="e735e-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="e735e-270">Startup.cs</span></span>

<span data-ttu-id="e735e-271">Contiene código que configura el comportamiento de la aplicación, como, por ejemplo, si se requiere consentimiento para las cookies.</span><span class="sxs-lookup"><span data-stu-id="e735e-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="e735e-272">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e735e-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e735e-273">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e735e-273">Additional resources</span></span>

* [<span data-ttu-id="e735e-274">Versión en YouTube de este tutorial</span><span class="sxs-lookup"><span data-stu-id="e735e-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="e735e-275">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="e735e-275">Next steps</span></span>

<span data-ttu-id="e735e-276">Pase al siguiente tutorial de la serie:</span><span class="sxs-lookup"><span data-stu-id="e735e-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e735e-277">Agregar un modelo</span><span class="sxs-lookup"><span data-stu-id="e735e-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
