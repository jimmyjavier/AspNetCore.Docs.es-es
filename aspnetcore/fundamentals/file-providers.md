---
<span data-ttu-id="c43fd-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-102">'Blazor'</span></span>
- <span data-ttu-id="c43fd-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-103">'Identity'</span></span>
- <span data-ttu-id="c43fd-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-105">'Razor'</span></span>
- <span data-ttu-id="c43fd-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="c43fd-107">Proveedores de archivo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c43fd-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="c43fd-108">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c43fd-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c43fd-109">ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.</span><span class="sxs-lookup"><span data-stu-id="c43fd-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="c43fd-110">Los proveedores de archivos se usan en el marco de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c43fd-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="c43fd-111">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="c43fd-111">For example:</span></span>

* <span data-ttu-id="c43fd-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="c43fd-113">El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c43fd-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="c43fd-114">[Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="c43fd-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="c43fd-115">Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.</span><span class="sxs-lookup"><span data-stu-id="c43fd-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="c43fd-116">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c43fd-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="c43fd-117">Interfaces de proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="c43fd-117">File Provider interfaces</span></span>

<span data-ttu-id="c43fd-118">La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="c43fd-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="c43fd-119">`IFileProvider` expone métodos para:</span><span class="sxs-lookup"><span data-stu-id="c43fd-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="c43fd-120">Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="c43fd-121">Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="c43fd-122">Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="c43fd-123">`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:</span><span class="sxs-lookup"><span data-stu-id="c43fd-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="c43fd-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)</span><span class="sxs-lookup"><span data-stu-id="c43fd-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="c43fd-125">Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="c43fd-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="c43fd-126">Se puede leer en el archivo mediante el método <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c43fd-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="c43fd-127">La aplicación de ejemplo *FileProviderSample* muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c43fd-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="c43fd-128">Implementaciones del proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="c43fd-128">File Provider implementations</span></span>

<span data-ttu-id="c43fd-129">En la tabla siguiente se enumeran las implementaciones de `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="c43fd-130">Implementación</span><span class="sxs-lookup"><span data-stu-id="c43fd-130">Implementation</span></span> | <span data-ttu-id="c43fd-131">Descripción</span><span class="sxs-lookup"><span data-stu-id="c43fd-131">Description</span></span> |
| ---
<span data-ttu-id="c43fd-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-133">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-133">'Blazor'</span></span>
- <span data-ttu-id="c43fd-134">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-134">'Identity'</span></span>
- <span data-ttu-id="c43fd-135">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-136">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-136">'Razor'</span></span>
- <span data-ttu-id="c43fd-137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-139">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-139">'Blazor'</span></span>
- <span data-ttu-id="c43fd-140">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-140">'Identity'</span></span>
- <span data-ttu-id="c43fd-141">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-142">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-142">'Razor'</span></span>
- <span data-ttu-id="c43fd-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-145">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-145">'Blazor'</span></span>
- <span data-ttu-id="c43fd-146">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-146">'Identity'</span></span>
- <span data-ttu-id="c43fd-147">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-148">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-148">'Razor'</span></span>
- <span data-ttu-id="c43fd-149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-151">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-151">'Blazor'</span></span>
- <span data-ttu-id="c43fd-152">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-152">'Identity'</span></span>
- <span data-ttu-id="c43fd-153">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-154">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-154">'Razor'</span></span>
- <span data-ttu-id="c43fd-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-157">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-157">'Blazor'</span></span>
- <span data-ttu-id="c43fd-158">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-158">'Identity'</span></span>
- <span data-ttu-id="c43fd-159">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-160">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-160">'Razor'</span></span>
- <span data-ttu-id="c43fd-161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-161">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-163">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-163">'Blazor'</span></span>
- <span data-ttu-id="c43fd-164">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-164">'Identity'</span></span>
- <span data-ttu-id="c43fd-165">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-166">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-166">'Razor'</span></span>
- <span data-ttu-id="c43fd-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-169">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-169">'Blazor'</span></span>
- <span data-ttu-id="c43fd-170">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-170">'Identity'</span></span>
- <span data-ttu-id="c43fd-171">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-172">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-172">'Razor'</span></span>
- <span data-ttu-id="c43fd-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-175">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-175">'Blazor'</span></span>
- <span data-ttu-id="c43fd-176">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-176">'Identity'</span></span>
- <span data-ttu-id="c43fd-177">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-178">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-178">'Razor'</span></span>
- <span data-ttu-id="c43fd-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-179">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-180">------ | | [CompositeFileProvider](#compositefileprovider) | Se utiliza para proporcionar acceso combinado a archivos y directorios de uno o más proveedores.</span><span class="sxs-lookup"><span data-stu-id="c43fd-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="c43fd-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Se utiliza para acceder a archivos incrustados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="c43fd-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="c43fd-182">| | [PhysicalFileProvider](#physicalfileprovider) | Se utiliza para acceder a los archivos físicos del sistema.</span><span class="sxs-lookup"><span data-stu-id="c43fd-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="c43fd-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="c43fd-183">PhysicalFileProvider</span></span>

<span data-ttu-id="c43fd-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico.</span><span class="sxs-lookup"><span data-stu-id="c43fd-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="c43fd-185">`PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="c43fd-186">Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="c43fd-187">El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c43fd-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="c43fd-188">Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso absoluta al directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.</span><span class="sxs-lookup"><span data-stu-id="c43fd-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="c43fd-189">Los patrones globales no se admiten en la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="c43fd-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="c43fd-190">El código siguiente muestra cómo usar `PhysicalFileProvider` para obtener el contenido del directorio y la información del archivo:</span><span class="sxs-lookup"><span data-stu-id="c43fd-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="c43fd-191">Tipos del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="c43fd-191">Types in the preceding example:</span></span>

* <span data-ttu-id="c43fd-192">`provider` es `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="c43fd-193">`contents` es `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="c43fd-194">`fileInfo` es `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="c43fd-195">El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo.</span><span class="sxs-lookup"><span data-stu-id="c43fd-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="c43fd-196">No se pueden pasar patrones globales al método `GetFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="c43fd-197">El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="c43fd-198">La aplicación de ejemplo *FileProviderSample* crea el proveedor en el método `Startup.ConfigureServices` con <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="c43fd-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="c43fd-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="c43fd-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="c43fd-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="c43fd-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="c43fd-201">`ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.</span><span class="sxs-lookup"><span data-stu-id="c43fd-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="c43fd-202">Para generar un manifiesto de los archivos incrustados:</span><span class="sxs-lookup"><span data-stu-id="c43fd-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="c43fd-203">Agregue el paquete de NuGet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) al proyecto.</span><span class="sxs-lookup"><span data-stu-id="c43fd-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="c43fd-204">Establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="c43fd-205">Especifique los archivos que desea incrustar con [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="c43fd-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="c43fd-206">Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="c43fd-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="c43fd-207">La aplicación de ejemplo *FileProviderSample* crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.</span><span class="sxs-lookup"><span data-stu-id="c43fd-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="c43fd-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c43fd-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="c43fd-209">Las sobrecargas adicionales le permiten:</span><span class="sxs-lookup"><span data-stu-id="c43fd-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="c43fd-210">Especificar una ruta de acceso de archivo relativa.</span><span class="sxs-lookup"><span data-stu-id="c43fd-210">Specify a relative file path.</span></span>
* <span data-ttu-id="c43fd-211">Definir el ámbito de archivos a la fecha de la última modificación.</span><span class="sxs-lookup"><span data-stu-id="c43fd-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="c43fd-212">Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.</span><span class="sxs-lookup"><span data-stu-id="c43fd-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="c43fd-213">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="c43fd-213">Overload</span></span> | <span data-ttu-id="c43fd-214">Descripción</span><span class="sxs-lookup"><span data-stu-id="c43fd-214">Description</span></span> |
| ---
<span data-ttu-id="c43fd-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-216">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-216">'Blazor'</span></span>
- <span data-ttu-id="c43fd-217">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-217">'Identity'</span></span>
- <span data-ttu-id="c43fd-218">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-219">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-219">'Razor'</span></span>
- <span data-ttu-id="c43fd-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-222">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-222">'Blazor'</span></span>
- <span data-ttu-id="c43fd-223">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-223">'Identity'</span></span>
- <span data-ttu-id="c43fd-224">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-225">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-225">'Razor'</span></span>
- <span data-ttu-id="c43fd-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-226">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-228">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-228">'Blazor'</span></span>
- <span data-ttu-id="c43fd-229">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-229">'Identity'</span></span>
- <span data-ttu-id="c43fd-230">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-231">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-231">'Razor'</span></span>
- <span data-ttu-id="c43fd-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-234">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-234">'Blazor'</span></span>
- <span data-ttu-id="c43fd-235">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-235">'Identity'</span></span>
- <span data-ttu-id="c43fd-236">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-237">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-237">'Razor'</span></span>
- <span data-ttu-id="c43fd-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-240">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-240">'Blazor'</span></span>
- <span data-ttu-id="c43fd-241">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-241">'Identity'</span></span>
- <span data-ttu-id="c43fd-242">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-243">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-243">'Razor'</span></span>
- <span data-ttu-id="c43fd-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-244">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Acepta un parámetro de ruta de acceso relativa `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="c43fd-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="c43fd-246">Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas.</span><span class="sxs-lookup"><span data-stu-id="c43fd-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="c43fd-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="c43fd-248">La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve.</span><span class="sxs-lookup"><span data-stu-id="c43fd-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="c43fd-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="c43fd-250">`manifestName` representa el nombre del recurso incrustado que contiene el manifiesto.</span><span class="sxs-lookup"><span data-stu-id="c43fd-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="c43fd-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="c43fd-251">CompositeFileProvider</span></span>

<span data-ttu-id="c43fd-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="c43fd-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="c43fd-253">Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="c43fd-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="c43fd-254">En la aplicación de ejemplo *FileProviderSample*, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="c43fd-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="c43fd-255">El código siguiente se encuentra en el método `Startup.ConfigureServices` del proyecto:</span><span class="sxs-lookup"><span data-stu-id="c43fd-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="c43fd-256">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="c43fd-256">Watch for changes</span></span>

<span data-ttu-id="c43fd-257">El método <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> proporciona un escenario para ver uno o más archivos o directorios para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="c43fd-258">El método `Watch` realiza las acciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="c43fd-258">The `Watch` method:</span></span>

* <span data-ttu-id="c43fd-259">Acepta una cadena de ruta de acceso a archivo, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.</span><span class="sxs-lookup"><span data-stu-id="c43fd-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="c43fd-260">Devuelve un valor <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="c43fd-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="c43fd-261">El token de cambio resultante expone:</span><span class="sxs-lookup"><span data-stu-id="c43fd-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="c43fd-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="c43fd-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="c43fd-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="c43fd-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="c43fd-264">Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio.</span><span class="sxs-lookup"><span data-stu-id="c43fd-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="c43fd-265">Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="c43fd-266">La aplicación de ejemplo *WatchConsole* escribe un mensaje cada vez que se modifica un archivo *.txt* en el directorio *TextFiles*:</span><span class="sxs-lookup"><span data-stu-id="c43fd-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="c43fd-267">Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="c43fd-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="c43fd-268">Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).</span><span class="sxs-lookup"><span data-stu-id="c43fd-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="c43fd-269">Patrones globales</span><span class="sxs-lookup"><span data-stu-id="c43fd-269">Glob patterns</span></span>

<span data-ttu-id="c43fd-270">Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*.</span><span class="sxs-lookup"><span data-stu-id="c43fd-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="c43fd-271">Especifique grupos de archivos con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="c43fd-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="c43fd-272">Los dos caracteres comodín son `*` y `**`:</span><span class="sxs-lookup"><span data-stu-id="c43fd-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="c43fd-273">Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="c43fd-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="c43fd-274">Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.</span><span class="sxs-lookup"><span data-stu-id="c43fd-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="c43fd-275">Coincide con cualquier elemento de varios niveles de directorios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="c43fd-276">Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="c43fd-277">En la tabla siguiente se proporcionan ejemplos comunes de patrones globales.</span><span class="sxs-lookup"><span data-stu-id="c43fd-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="c43fd-278">Modelo</span><span class="sxs-lookup"><span data-stu-id="c43fd-278">Pattern</span></span>  |<span data-ttu-id="c43fd-279">Descripción</span><span class="sxs-lookup"><span data-stu-id="c43fd-279">Description</span></span>  |
|---
<span data-ttu-id="c43fd-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-281">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-281">'Blazor'</span></span>
- <span data-ttu-id="c43fd-282">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-282">'Identity'</span></span>
- <span data-ttu-id="c43fd-283">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-284">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-284">'Razor'</span></span>
- <span data-ttu-id="c43fd-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-287">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-287">'Blazor'</span></span>
- <span data-ttu-id="c43fd-288">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-288">'Identity'</span></span>
- <span data-ttu-id="c43fd-289">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-290">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-290">'Razor'</span></span>
- <span data-ttu-id="c43fd-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-291">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-293">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-293">'Blazor'</span></span>
- <span data-ttu-id="c43fd-294">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-294">'Identity'</span></span>
- <span data-ttu-id="c43fd-295">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-296">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-296">'Razor'</span></span>
- <span data-ttu-id="c43fd-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-299">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-299">'Blazor'</span></span>
- <span data-ttu-id="c43fd-300">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-300">'Identity'</span></span>
- <span data-ttu-id="c43fd-301">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-302">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-302">'Razor'</span></span>
- <span data-ttu-id="c43fd-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-303">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-304">-----|
|`directory/file.txt`|Coincide con un archivo concreto en un directorio específico.| |`directory/*.txt`|Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.| |`directory/*/appsettings.json`|Coincide con todos los archivos *appsettings.json* que estén en directorios exactamente un nivel por debajo de la carpeta *directorio*.| |`directory/**/*.txt`|Coincide con todos los archivos que tengan la extensión *.txt* y se encuentren en cualquier lugar de la carpeta *directorio*.|</span><span class="sxs-lookup"><span data-stu-id="c43fd-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c43fd-305">ASP.NET Core abstrae el acceso al sistema de archivos mediante el uso de proveedores de archivos.</span><span class="sxs-lookup"><span data-stu-id="c43fd-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="c43fd-306">Los proveedores de archivos se usan en el marco de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c43fd-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="c43fd-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> expone la [raíz del contenido](xref:fundamentals/index#content-root) y la [raíz web](xref:fundamentals/index#web-root) de la aplicación como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="c43fd-308">El [middleware de archivos estáticos](xref:fundamentals/static-files) usa proveedores de archivos para buscar archivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c43fd-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="c43fd-309">[Razor](xref:mvc/views/razor) usa proveedores de archivos para localizar páginas y vistas.</span><span class="sxs-lookup"><span data-stu-id="c43fd-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="c43fd-310">Las herramientas de .NET Core usan proveedores de archivos y patrones globales para especificar los archivos que deben publicarse.</span><span class="sxs-lookup"><span data-stu-id="c43fd-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="c43fd-311">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c43fd-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="c43fd-312">Interfaces de proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="c43fd-312">File Provider interfaces</span></span>

<span data-ttu-id="c43fd-313">La interfaz principal es <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="c43fd-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="c43fd-314">`IFileProvider` expone métodos para:</span><span class="sxs-lookup"><span data-stu-id="c43fd-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="c43fd-315">Obtenga la información del archivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="c43fd-316">Obtenga la información del directorio (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="c43fd-317">Configure las notificaciones de cambio (mediante <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="c43fd-318">`IFileInfo` proporciona métodos y propiedades para trabajar con archivos:</span><span class="sxs-lookup"><span data-stu-id="c43fd-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="c43fd-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en bytes)</span><span class="sxs-lookup"><span data-stu-id="c43fd-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="c43fd-320">Fecha <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="c43fd-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="c43fd-321">Puede leer del archivo mediante el método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="c43fd-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="c43fd-322">La aplicación de ejemplo muestra cómo configurar un proveedor de archivos en `Startup.ConfigureServices` para su uso en toda la aplicación a través de la [inserción de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c43fd-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="c43fd-323">Implementaciones del proveedor de archivos</span><span class="sxs-lookup"><span data-stu-id="c43fd-323">File Provider implementations</span></span>

<span data-ttu-id="c43fd-324">Hay tres implementaciones de `IFileProvider` disponibles.</span><span class="sxs-lookup"><span data-stu-id="c43fd-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="c43fd-325">Implementación</span><span class="sxs-lookup"><span data-stu-id="c43fd-325">Implementation</span></span> | <span data-ttu-id="c43fd-326">Descripción</span><span class="sxs-lookup"><span data-stu-id="c43fd-326">Description</span></span> |
| ---
<span data-ttu-id="c43fd-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-328">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-328">'Blazor'</span></span>
- <span data-ttu-id="c43fd-329">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-329">'Identity'</span></span>
- <span data-ttu-id="c43fd-330">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-331">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-331">'Razor'</span></span>
- <span data-ttu-id="c43fd-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-334">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-334">'Blazor'</span></span>
- <span data-ttu-id="c43fd-335">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-335">'Identity'</span></span>
- <span data-ttu-id="c43fd-336">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-337">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-337">'Razor'</span></span>
- <span data-ttu-id="c43fd-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-340">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-340">'Blazor'</span></span>
- <span data-ttu-id="c43fd-341">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-341">'Identity'</span></span>
- <span data-ttu-id="c43fd-342">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-343">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-343">'Razor'</span></span>
- <span data-ttu-id="c43fd-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-346">'Blazor'</span></span>
- <span data-ttu-id="c43fd-347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-347">'Identity'</span></span>
- <span data-ttu-id="c43fd-348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-349">'Razor'</span></span>
- <span data-ttu-id="c43fd-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-352">'Blazor'</span></span>
- <span data-ttu-id="c43fd-353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-353">'Identity'</span></span>
- <span data-ttu-id="c43fd-354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-355">'Razor'</span></span>
- <span data-ttu-id="c43fd-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-356">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-358">'Blazor'</span></span>
- <span data-ttu-id="c43fd-359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-359">'Identity'</span></span>
- <span data-ttu-id="c43fd-360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-361">'Razor'</span></span>
- <span data-ttu-id="c43fd-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-364">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-364">'Blazor'</span></span>
- <span data-ttu-id="c43fd-365">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-365">'Identity'</span></span>
- <span data-ttu-id="c43fd-366">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-367">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-367">'Razor'</span></span>
- <span data-ttu-id="c43fd-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-370">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-370">'Blazor'</span></span>
- <span data-ttu-id="c43fd-371">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-371">'Identity'</span></span>
- <span data-ttu-id="c43fd-372">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-373">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-373">'Razor'</span></span>
- <span data-ttu-id="c43fd-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-374">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | El proveedor físico se utiliza para acceder a los archivos físicos del sistema.</span><span class="sxs-lookup"><span data-stu-id="c43fd-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="c43fd-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | El proveedor insertado de manifiestos se utiliza para tener acceder a archivos insertados en ensamblados.</span><span class="sxs-lookup"><span data-stu-id="c43fd-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="c43fd-377">| | [CompositeFileProvider](#compositefileprovider) | El proveedor compuesto se utiliza para proporcionar acceso combinado a archivos y directorios de uno o más proveedores.</span><span class="sxs-lookup"><span data-stu-id="c43fd-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="c43fd-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="c43fd-378">PhysicalFileProvider</span></span>

<span data-ttu-id="c43fd-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> proporciona acceso al sistema de archivos físico.</span><span class="sxs-lookup"><span data-stu-id="c43fd-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="c43fd-380">`PhysicalFileProvider` usa el tipo <xref:System.IO.File?displayProperty=fullName> (para el proveedor físico) y define el ámbito de todas las rutas de acceso a un directorio y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="c43fd-381">Esta definición de ámbito impide el acceso al sistema de archivos fuera del directorio especificado y sus elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="c43fd-382">El escenario más común para crear y usar `PhysicalFileProvider` es solicitar `IFileProvider` en un constructor mediante la [inyección de dependencias](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c43fd-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="c43fd-383">Al crear una instancia de este proveedor directamente, se requiere una ruta de acceso del directorio, que actúa como la ruta de acceso base para todas las solicitudes realizadas usando el proveedor.</span><span class="sxs-lookup"><span data-stu-id="c43fd-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="c43fd-384">El código siguiente muestra cómo crear `PhysicalFileProvider` y usarlo para obtener el contenido del directorio y la información del archivo:</span><span class="sxs-lookup"><span data-stu-id="c43fd-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="c43fd-385">Tipos del ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="c43fd-385">Types in the preceding example:</span></span>

* <span data-ttu-id="c43fd-386">`provider` es `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="c43fd-387">`contents` es `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="c43fd-388">`fileInfo` es `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="c43fd-389">El proveedor de archivos puede usarse para iterar por el directorio especificado por `applicationRoot` o llamar a `GetFileInfo` para obtener información de un archivo.</span><span class="sxs-lookup"><span data-stu-id="c43fd-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="c43fd-390">El proveedor de archivos no tiene acceso fuera del directorio `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="c43fd-391">La aplicación de ejemplo crea el proveedor en la clase `Startup.ConfigureServices` de la aplicación mediante [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="c43fd-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="c43fd-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="c43fd-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="c43fd-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se utiliza para acceder a archivos insertados dentro de ensamblados.</span><span class="sxs-lookup"><span data-stu-id="c43fd-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="c43fd-394">`ManifestEmbeddedFileProvider` utiliza un manifiesto compilado en el ensamblado para reconstruir las rutas de acceso originales de los archivos incrustados.</span><span class="sxs-lookup"><span data-stu-id="c43fd-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="c43fd-395">Para generar un manifiesto de los archivos incrustados, establezca la propiedad `<GenerateEmbeddedFilesManifest>` en `true`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="c43fd-396">Especifique los archivos que desea incrustar con [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="c43fd-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="c43fd-397">Use [patrones globales](#glob-patterns) para especificar uno o varios archivos para incrustar en el ensamblado.</span><span class="sxs-lookup"><span data-stu-id="c43fd-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="c43fd-398">La aplicación de ejemplo crea `ManifestEmbeddedFileProvider` y pasa el ensamblado que se está ejecutando actualmente a su constructor.</span><span class="sxs-lookup"><span data-stu-id="c43fd-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="c43fd-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c43fd-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="c43fd-400">Las sobrecargas adicionales le permiten:</span><span class="sxs-lookup"><span data-stu-id="c43fd-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="c43fd-401">Especificar una ruta de acceso de archivo relativa.</span><span class="sxs-lookup"><span data-stu-id="c43fd-401">Specify a relative file path.</span></span>
* <span data-ttu-id="c43fd-402">Definir el ámbito de archivos a la fecha de la última modificación.</span><span class="sxs-lookup"><span data-stu-id="c43fd-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="c43fd-403">Asignar nombre al recurso incrustado que contiene el manifiesto del archivo incrustado.</span><span class="sxs-lookup"><span data-stu-id="c43fd-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="c43fd-404">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="c43fd-404">Overload</span></span> | <span data-ttu-id="c43fd-405">Descripción</span><span class="sxs-lookup"><span data-stu-id="c43fd-405">Description</span></span> |
| ---
<span data-ttu-id="c43fd-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-407">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-407">'Blazor'</span></span>
- <span data-ttu-id="c43fd-408">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-408">'Identity'</span></span>
- <span data-ttu-id="c43fd-409">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-410">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-410">'Razor'</span></span>
- <span data-ttu-id="c43fd-411">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-413">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-413">'Blazor'</span></span>
- <span data-ttu-id="c43fd-414">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-414">'Identity'</span></span>
- <span data-ttu-id="c43fd-415">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-416">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-416">'Razor'</span></span>
- <span data-ttu-id="c43fd-417">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-417">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-419">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-419">'Blazor'</span></span>
- <span data-ttu-id="c43fd-420">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-420">'Identity'</span></span>
- <span data-ttu-id="c43fd-421">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-422">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-422">'Razor'</span></span>
- <span data-ttu-id="c43fd-423">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-425">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-425">'Blazor'</span></span>
- <span data-ttu-id="c43fd-426">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-426">'Identity'</span></span>
- <span data-ttu-id="c43fd-427">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-428">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-428">'Razor'</span></span>
- <span data-ttu-id="c43fd-429">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="c43fd-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c43fd-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c43fd-431">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-431">'Blazor'</span></span>
- <span data-ttu-id="c43fd-432">"Identity"</span><span class="sxs-lookup"><span data-stu-id="c43fd-432">'Identity'</span></span>
- <span data-ttu-id="c43fd-433">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="c43fd-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="c43fd-434">"Razor"</span><span class="sxs-lookup"><span data-stu-id="c43fd-434">'Razor'</span></span>
- <span data-ttu-id="c43fd-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="c43fd-435">'SignalR' uid:</span></span> 

<span data-ttu-id="c43fd-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Acepta un parámetro de ruta de acceso relativa `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="c43fd-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="c43fd-437">Especifique `root` para definir el ámbito de las llamadas en <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> en aquellos recursos que se encuentran bajo las rutas de acceso proporcionadas.</span><span class="sxs-lookup"><span data-stu-id="c43fd-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="c43fd-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Acepta un parámetro de ruta de acceso relativa `root` opcional y un parámetro de fecha `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="c43fd-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="c43fd-439">La fecha `lastModified` define el ámbito de la última fecha de modificación para las instancias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> que <xref:Microsoft.Extensions.FileProviders.IFileProvider> devuelve.</span><span class="sxs-lookup"><span data-stu-id="c43fd-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="c43fd-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Acepta una ruta de acceso relativa `root` opcional, una fecha `lastModified` y parámetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="c43fd-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="c43fd-441">`manifestName` representa el nombre del recurso incrustado que contiene el manifiesto.</span><span class="sxs-lookup"><span data-stu-id="c43fd-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="c43fd-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="c43fd-442">CompositeFileProvider</span></span>

<span data-ttu-id="c43fd-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instancias de `IFileProvider`, y expone una única interfaz para trabajar con archivos de varios proveedores.</span><span class="sxs-lookup"><span data-stu-id="c43fd-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="c43fd-444">Al crear `CompositeFileProvider`, se pasan una o varias instancias de `IFileProvider` a su constructor.</span><span class="sxs-lookup"><span data-stu-id="c43fd-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="c43fd-445">En la aplicación de ejemplo, `PhysicalFileProvider` y `ManifestEmbeddedFileProvider` proporcionan archivos a un `CompositeFileProvider` registrado en el contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="c43fd-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="c43fd-446">Observación de cambios</span><span class="sxs-lookup"><span data-stu-id="c43fd-446">Watch for changes</span></span>

<span data-ttu-id="c43fd-447">El método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona un escenario para ver uno o más archivos o directorios para detectar cambios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="c43fd-448">`Watch` acepta una cadena de ruta de acceso, que puede usar [patrones globales](#glob-patterns) para especificar varios archivos.</span><span class="sxs-lookup"><span data-stu-id="c43fd-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="c43fd-449">`Watch` devuelve un valor de <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="c43fd-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="c43fd-450">El token de cambio expone:</span><span class="sxs-lookup"><span data-stu-id="c43fd-450">The change token exposes:</span></span>

* <span data-ttu-id="c43fd-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: una propiedad que se puede inspeccionar para determinar si se ha producido un cambio.</span><span class="sxs-lookup"><span data-stu-id="c43fd-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="c43fd-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: se llama cuando se detectan cambios en la cadena de ruta de acceso especificada.</span><span class="sxs-lookup"><span data-stu-id="c43fd-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="c43fd-453">Cada token de cambio solo llama a su devolución de llamada asociada en respuesta a un único cambio.</span><span class="sxs-lookup"><span data-stu-id="c43fd-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="c43fd-454">Para habilitar la supervisión constante, puede usar <xref:System.Threading.Tasks.TaskCompletionSource`1> (como se muestra a continuación) o volver a crear instancias de `IChangeToken` en respuesta a los cambios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="c43fd-455">En la aplicación de ejemplo, la aplicación de consola *WatchConsole* se configura para mostrar un mensaje cada vez que se modifica un archivo de texto:</span><span class="sxs-lookup"><span data-stu-id="c43fd-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="c43fd-456">Algunos sistemas de archivos, como contenedores de Docker y recursos compartidos de red, no pueden enviar notificaciones de cambio de forma confiable.</span><span class="sxs-lookup"><span data-stu-id="c43fd-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="c43fd-457">Establezca la variable de entorno `DOTNET_USE_POLLING_FILE_WATCHER` en `1` o `true` para sondear el sistema de archivos en busca de cambios cada cuatro segundos (no configurable).</span><span class="sxs-lookup"><span data-stu-id="c43fd-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="c43fd-458">Patrones globales</span><span class="sxs-lookup"><span data-stu-id="c43fd-458">Glob patterns</span></span>

<span data-ttu-id="c43fd-459">Las rutas de acceso del sistema de archivos utilizan patrones de caracteres comodín denominados *patrones globales*.</span><span class="sxs-lookup"><span data-stu-id="c43fd-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="c43fd-460">Especifique grupos de archivos con estos patrones.</span><span class="sxs-lookup"><span data-stu-id="c43fd-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="c43fd-461">Los dos caracteres comodín son `*` y `**`:</span><span class="sxs-lookup"><span data-stu-id="c43fd-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="c43fd-462">Coincide con cualquier elemento del nivel de carpeta actual, con cualquier nombre de archivo o con cualquier extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="c43fd-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="c43fd-463">Las coincidencias finalizan con los caracteres `/` y `.` en la ruta de acceso de archivo.</span><span class="sxs-lookup"><span data-stu-id="c43fd-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="c43fd-464">Coincide con cualquier elemento de varios niveles de directorios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="c43fd-465">Puede usarse para coincidir de forma recursiva con muchos archivos dentro de una jerarquía de directorios.</span><span class="sxs-lookup"><span data-stu-id="c43fd-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="c43fd-466">**Ejemplos de patrones globales**</span><span class="sxs-lookup"><span data-stu-id="c43fd-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="c43fd-467">Coincide con un archivo concreto en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="c43fd-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="c43fd-468">Coincide con todos los archivos que tengan la extensión *.txt* en un directorio específico.</span><span class="sxs-lookup"><span data-stu-id="c43fd-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="c43fd-469">Coincide con todos los archivos `appsettings.json` que estén en directorios exactamente un nivel por debajo de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="c43fd-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="c43fd-470">Coincide con todos los archivos que tengan la extensión *.txt* y se encuentren en cualquier lugar de la carpeta *directorio*.</span><span class="sxs-lookup"><span data-stu-id="c43fd-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
