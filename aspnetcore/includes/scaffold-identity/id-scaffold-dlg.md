<span data-ttu-id="be59a-101">Ejecute el scaffolding de identidad:</span><span class="sxs-lookup"><span data-stu-id="be59a-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="be59a-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be59a-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="be59a-103">En **Explorador de soluciones**, haga clic con el botón derecho en el proyecto > **Agregar**  >  **nuevo elemento con scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="be59a-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="be59a-104">En el panel izquierdo del cuadro de diálogo **Agregar nuevo elemento con scaffolding** , seleccione **identidad**  >  **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="be59a-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="be59a-105">En el cuadro de diálogo **Agregar identidad** , seleccione las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="be59a-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="be59a-106">Seleccione la página de diseño existente o el archivo de diseño se sobrescribirá con un marcado incorrecto:</span><span class="sxs-lookup"><span data-stu-id="be59a-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="be59a-107">`~/Pages/Shared/_Layout.cshtml`por Razor Pages</span><span class="sxs-lookup"><span data-stu-id="be59a-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="be59a-108">`~/Views/Shared/_Layout.cshtml`para proyectos de MVC</span><span class="sxs-lookup"><span data-stu-id="be59a-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="be59a-109">Las aplicaciones de servidor increíbles que se crean a partir de la plantilla de servidor de extraordinarias ( `blazorserver` ) no están configuradas para Razor pages o MVC de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="be59a-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="be59a-110">Deje la entrada página de diseño en blanco.</span><span class="sxs-lookup"><span data-stu-id="be59a-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="be59a-111">Seleccione el **+** botón para crear una nueva **clase de contexto de datos**.</span><span class="sxs-lookup"><span data-stu-id="be59a-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="be59a-112">Acepte el valor predeterminado o especifique una clase (por ejemplo, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="be59a-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="be59a-113">Seleccione **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="be59a-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="be59a-114">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="be59a-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="be59a-115">Si no ha instalado previamente el scaffolding de ASP.NET Core, instálelo ahora:</span><span class="sxs-lookup"><span data-stu-id="be59a-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="be59a-116">Agregue las referencias de paquete de NuGet necesarias al archivo de proyecto (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="be59a-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="be59a-117">Ejecute los siguientes comandos en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="be59a-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="be59a-118">Ejecute el siguiente comando para enumerar las opciones del scaffolding de identidad:</span><span class="sxs-lookup"><span data-stu-id="be59a-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="be59a-119">En la carpeta del proyecto, ejecute el scaffolding de identidad con las opciones que desee.</span><span class="sxs-lookup"><span data-stu-id="be59a-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="be59a-120">Por ejemplo, para configurar la identidad con la interfaz de usuario predeterminada y el número mínimo de archivos, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="be59a-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
