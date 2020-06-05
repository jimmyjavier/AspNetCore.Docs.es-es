::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90a4e-101">Ejecute el scaffolding de identidad:</span><span class="sxs-lookup"><span data-stu-id="90a4e-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90a4e-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90a4e-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="90a4e-103">En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar** > **nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="90a4e-104">En el panel izquierdo del cuadro de diálogo **Agregar scaffold** , seleccione **Identity** > **Add (agregar**identidad).</span><span class="sxs-lookup"><span data-stu-id="90a4e-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="90a4e-105">En el cuadro de diálogo **Agregar identidad** , seleccione las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="90a4e-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="90a4e-106">Seleccione la página de diseño existente para que el archivo de diseño no se sobrescriba con un marcado incorrecto.</span><span class="sxs-lookup"><span data-stu-id="90a4e-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="90a4e-107">Cuando se selecciona un archivo \* \_ layout. cshtml\* existente, **no** se sobrescribe.</span><span class="sxs-lookup"><span data-stu-id="90a4e-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="90a4e-108">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="90a4e-108">For example:</span></span>
    * <span data-ttu-id="90a4e-109">`~/Pages/Shared/_Layout.cshtml`para los proyectos de servidor Razor Pages o increíble con la infraestructura de Razor Pages existente</span><span class="sxs-lookup"><span data-stu-id="90a4e-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="90a4e-110">`~/Views/Shared/_Layout.cshtml`para proyectos de MVC o proyectos de servidor increíbles con la infraestructura MVC existente</span><span class="sxs-lookup"><span data-stu-id="90a4e-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="90a4e-111">Para usar el contexto de datos existente, seleccione al menos un archivo para invalidar.</span><span class="sxs-lookup"><span data-stu-id="90a4e-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="90a4e-112">Debe seleccionar al menos un archivo para agregar el contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="90a4e-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="90a4e-113">Seleccione la clase de contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="90a4e-113">Select your data context class.</span></span>
  * <span data-ttu-id="90a4e-114">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-114">Select **Add**.</span></span>
* <span data-ttu-id="90a4e-115">Para crear un nuevo contexto de usuario y, posiblemente, crear una clase de usuario personalizada para la identidad:</span><span class="sxs-lookup"><span data-stu-id="90a4e-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="90a4e-116">Seleccione el **+** botón para crear una nueva **clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="90a4e-117">Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="90a4e-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="90a4e-118">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-118">Select **Add**.</span></span>

<span data-ttu-id="90a4e-119">Nota: Si va a crear un nuevo contexto de usuario, no tiene que seleccionar un archivo para invalidar.</span><span class="sxs-lookup"><span data-stu-id="90a4e-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="90a4e-120">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="90a4e-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="90a4e-121">Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:</span><span class="sxs-lookup"><span data-stu-id="90a4e-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="90a4e-122">Agregue las referencias de paquete de NuGet necesarias al archivo de proyecto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="90a4e-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="90a4e-123">Ejecute los siguientes comandos en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="90a4e-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="90a4e-124">Ejecute el siguiente comando para enumerar las opciones del scaffolding de identidad:</span><span class="sxs-lookup"><span data-stu-id="90a4e-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="90a4e-125">En la carpeta del proyecto, ejecute el scaffolding de identidad con las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="90a4e-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="90a4e-126">Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="90a4e-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="90a4e-127">Use el nombre completo correcto para el contexto de base de BD:</span><span class="sxs-lookup"><span data-stu-id="90a4e-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="90a4e-128">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="90a4e-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="90a4e-129">Al usar PowerShell, use el carácter de escape de punto y coma en la lista de archivos o ponga la lista de archivos entre comillas dobles.</span><span class="sxs-lookup"><span data-stu-id="90a4e-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="90a4e-130">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="90a4e-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="90a4e-131">Si ejecuta el scaffolding de identidad sin especificar la `--files` marca o la `--useDefaultUI` marca, se crearán todas las páginas de la interfaz de usuario de identidad disponibles en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="90a4e-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90a4e-132">Ejecute el scaffolding de identidad:</span><span class="sxs-lookup"><span data-stu-id="90a4e-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90a4e-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90a4e-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="90a4e-134">En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar** > **nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="90a4e-135">En el panel izquierdo del cuadro de diálogo **Agregar scaffold** , seleccione **Identity** > **Add (agregar**identidad).</span><span class="sxs-lookup"><span data-stu-id="90a4e-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="90a4e-136">En el cuadro de diálogo **Agregar identidad** , seleccione las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="90a4e-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="90a4e-137">Seleccione la página de diseño existente o el archivo de diseño se sobrescribirá con un marcado incorrecto.</span><span class="sxs-lookup"><span data-stu-id="90a4e-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="90a4e-138">Cuando se selecciona un archivo \* \_ layout. cshtml\* existente, **no** se sobrescribe.</span><span class="sxs-lookup"><span data-stu-id="90a4e-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="90a4e-139">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="90a4e-139">For example:</span></span>
    * <span data-ttu-id="90a4e-140">`~/Pages/Shared/_Layout.cshtml`por Razor Pages</span><span class="sxs-lookup"><span data-stu-id="90a4e-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="90a4e-141">`~/Views/Shared/_Layout.cshtml`para proyectos de MVC</span><span class="sxs-lookup"><span data-stu-id="90a4e-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="90a4e-142">Para usar el contexto de datos existente, seleccione al menos un archivo para invalidar.</span><span class="sxs-lookup"><span data-stu-id="90a4e-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="90a4e-143">Debe seleccionar al menos un archivo para agregar el contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="90a4e-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="90a4e-144">Seleccione la clase de contexto de datos.</span><span class="sxs-lookup"><span data-stu-id="90a4e-144">Select your data context class.</span></span>
  * <span data-ttu-id="90a4e-145">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-145">Select **Add**.</span></span>
* <span data-ttu-id="90a4e-146">Para crear un nuevo contexto de usuario y, posiblemente, crear una clase de usuario personalizada para la identidad:</span><span class="sxs-lookup"><span data-stu-id="90a4e-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="90a4e-147">Seleccione el **+** botón para crear una nueva **clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="90a4e-148">Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="90a4e-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="90a4e-149">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="90a4e-149">Select **Add**.</span></span>

<span data-ttu-id="90a4e-150">Nota: Si va a crear un nuevo contexto de usuario, no tiene que seleccionar un archivo para invalidar.</span><span class="sxs-lookup"><span data-stu-id="90a4e-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="90a4e-151">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="90a4e-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="90a4e-152">Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:</span><span class="sxs-lookup"><span data-stu-id="90a4e-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="90a4e-153">Agregue una referencia de paquete a [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) al archivo de proyecto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="90a4e-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="90a4e-154">Ejecute los siguientes comandos en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="90a4e-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="90a4e-155">Ejecute el siguiente comando para enumerar las opciones del scaffolding de identidad:</span><span class="sxs-lookup"><span data-stu-id="90a4e-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="90a4e-156">En la carpeta del proyecto, ejecute el scaffolding de identidad con las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="90a4e-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="90a4e-157">Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="90a4e-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="90a4e-158">Use el nombre completo correcto para el contexto de base de BD:</span><span class="sxs-lookup"><span data-stu-id="90a4e-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="90a4e-159">PowerShell usa punto y coma como separador de comandos.</span><span class="sxs-lookup"><span data-stu-id="90a4e-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="90a4e-160">Al usar PowerShell, use el carácter de escape de punto y coma en la lista de archivos o ponga la lista de archivos entre comillas dobles.</span><span class="sxs-lookup"><span data-stu-id="90a4e-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="90a4e-161">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="90a4e-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="90a4e-162">Si ejecuta el scaffolding de identidad sin especificar la `--files` marca o la `--useDefaultUI` marca, se crearán todas las páginas de la interfaz de usuario de identidad disponibles en el proyecto.</span><span class="sxs-lookup"><span data-stu-id="90a4e-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
