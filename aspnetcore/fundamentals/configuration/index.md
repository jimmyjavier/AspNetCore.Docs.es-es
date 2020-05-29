---
<span data-ttu-id="eaa1a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-102">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-103">'Identity'</span></span>
- <span data-ttu-id="eaa1a-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-105">'Razor'</span></span>
- <span data-ttu-id="eaa1a-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="eaa1a-107">Configuración en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa1a-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="eaa1a-108">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa1a-109">La configuración de ASP.NET Core se realiza mediante uno o varios [proveedores de configuración](#cp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="eaa1a-110">Los proveedores de configuración leen los datos sobre los ajustes de los pares clave-valor mediante distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="eaa1a-111">Archivos de configuración, como *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="eaa1a-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="eaa1a-112">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-112">Environment variables</span></span>
* <span data-ttu-id="eaa1a-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="eaa1a-113">Azure Key Vault</span></span>
* <span data-ttu-id="eaa1a-114">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="eaa1a-114">Azure App Configuration</span></span>
* <span data-ttu-id="eaa1a-115">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-115">Command-line arguments</span></span>
* <span data-ttu-id="eaa1a-116">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="eaa1a-117">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="eaa1a-117">Directory files</span></span>
* <span data-ttu-id="eaa1a-118">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="eaa1a-118">In-memory .NET objects</span></span>

<span data-ttu-id="eaa1a-119">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa1a-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="eaa1a-120">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="eaa1a-120">Default configuration</span></span>

<span data-ttu-id="eaa1a-121">Las aplicaciones web de ASP.NET Core creadas con [dotnet new](/dotnet/core/tools/dotnet-new) o con Visual Studio generan el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="eaa1a-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="eaa1a-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  agrega un elemento `IConfiguration` existente como origen.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="eaa1a-124">En el caso de una configuración predeterminada, agrega la configuración del [host](#hvac) y lo establece como el primer origen para la configuración de la _aplicación_.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="eaa1a-125">[appsettings.json](#appsettingsjson) con el [proveedor de configuración JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="eaa1a-126">*appsettings.* `Environment` *.json* con el [proveedor de configuración JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="eaa1a-127">Por ejemplo, *appsettings*.***Production***.*json* y *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="eaa1a-128">[Secretos de la aplicación](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="eaa1a-129">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="eaa1a-130">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="eaa1a-131">Los proveedores de configuración que se agregan posteriormente invalidan los ajustes de configuración de la clave anteriores.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="eaa1a-132">Por ejemplo, si se establece `MyKey` tanto en *appsettings.json* como en el entorno, se usa el valor del entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="eaa1a-133">Con los proveedores de configuración predeterminados, el [proveedor de configuración de línea de comandos](#command-line-configuration-provider) reemplaza al resto de proveedores.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="eaa1a-134">Para obtener más información sobre `CreateDefaultBuilder`, consulte el artículo [Configuración predeterminada del generador](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="eaa1a-135">El código siguiente muestra los proveedores de configuración habilitados en el orden en el que se han agregado:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="eaa1a-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="eaa1a-136">appsettings.json</span></span>

<span data-ttu-id="eaa1a-137">Fíjese en el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="eaa1a-138">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-139">El elemento <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> predeterminado carga la configuración en el siguiente orden:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="eaa1a-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="eaa1a-140">*appsettings.json*</span></span>
1. <span data-ttu-id="eaa1a-141">*appsettings.* `Environment` *.json*: por ejemplo, los archivos *appsettings*.***Production***.*json* y *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="eaa1a-142">La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="eaa1a-143">Para obtener más información, vea <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="eaa1a-144">Los valores de *appsettings*.`Environment`.*json* invalidan las claves de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="eaa1a-145">Por ejemplo, de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-145">For example, by default:</span></span>

* <span data-ttu-id="eaa1a-146">En desarrollo, la configuración de *appsettings*.***Development***.*json* sobrescribe los valores de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="eaa1a-147">En producción, la configuración de *appsettings*.***Production***.*json* sobrescribe los valores de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="eaa1a-148">Por ejemplo, al implementar la aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="eaa1a-149">Enlace de datos de configuración jerárquica mediante el patrón de opciones</span><span class="sxs-lookup"><span data-stu-id="eaa1a-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="eaa1a-150">Si usa la configuración [predeterminada](#default), los archivos *appsettings.json* y *appsettings.* `Environment` *.json* están habilitados con [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="eaa1a-151">Los cambios realizados en los archivos *appsettings.json* y *appsettings.* `Environment` *.json* ***después*** de iniciar la aplicación los lee el [proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="eaa1a-152">Consulte la sección [Proveedor de configuración JSON](#jcp) en este artículo para obtener información sobre cómo agregar archivos de configuración JSON adicionales.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="eaa1a-153">Administrador de seguridad y secretos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-153">Security and secret manager</span></span>

<span data-ttu-id="eaa1a-154">Directrices para los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="eaa1a-155">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="eaa1a-156">Se puede usar el [administrador de secretos](xref:security/app-secrets) para almacenar secretos en entornos de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="eaa1a-157">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="eaa1a-158">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="eaa1a-159">De forma [predeterminada](#default), el [administrador de secretos](xref:security/app-secrets) lee los ajustes de configuración después de los archivos *appsettings.json* y *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="eaa1a-160">Para obtener más información sobre cómo almacenar contraseñas u otros datos confidenciales consulte:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="eaa1a-161"><xref:security/app-secrets>:  incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="eaa1a-162">El administrador de secretos usa el [proveedor de configuración de archivo](#fcp) para almacenar secretos de usuario en un archivo JSON en el sistema local.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="eaa1a-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="eaa1a-164">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="eaa1a-165">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-165">Environment variables</span></span>

<span data-ttu-id="eaa1a-166">Al usar la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de los pares clave-valor de la variable de entorno después de leer *appsettings.json*, *appsettings.* `Environment` *.json* y el [administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="eaa1a-167">Por lo tanto, los valores de clave que se leen del entorno reemplazan los valores que se leen de *appsettings.json*, *appsettings.* `Environment` *.json* y del administrador de secretos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="eaa1a-168">Los siguientes comandos `set`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-168">The following `set` commands:</span></span>

* <span data-ttu-id="eaa1a-169">Establecen las claves de entorno y los valores del [ejemplo anterior](#appsettingsjson) en Windows.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="eaa1a-170">Prueban la configuración al usar la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="eaa1a-171">El comando `dotnet run` debe ejecutarse en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="eaa1a-172">Los ajustes de configuración del entorno anteriores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-172">The preceding environment settings:</span></span>

* <span data-ttu-id="eaa1a-173">Solo se establecen en procesos iniciados desde la ventana de comandos en la que se establecieron.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="eaa1a-174">No los podrán leer los exploradores que se inician con Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="eaa1a-175">Los siguientes comandos [setx](/windows-server/administration/windows-commands/setx) se pueden usar para establecer las claves de entorno y los valores en Windows.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="eaa1a-176">A diferencia de `set`, la configuración de `setx` se conserva.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="eaa1a-177">`/M` establece la variable en el entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="eaa1a-178">Si no se usa el modificador `/M`, se establece una variable de entorno de usuario.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="eaa1a-179">Para comprobar que los comandos anteriores invalidan *appsettings.json* y *appsettings.* `Environment` *.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="eaa1a-180">Con Visual Studio: salga y reinicie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="eaa1a-181">Con la CLI: abra una nueva ventana de comandos y escriba `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="eaa1a-182">Llame a <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> con una cadena para especificar un prefijo para las variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="eaa1a-183">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-183">In the preceding code:</span></span>

* <span data-ttu-id="eaa1a-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="eaa1a-185">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="eaa1a-186">Las variables de entorno establecidas con el prefijo `MyCustomPrefix_` invalidan los proveedores de configuración [predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="eaa1a-187">Esto incluye las variables de entorno sin el prefijo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="eaa1a-188">El prefijo se quita cuando se leen los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="eaa1a-189">Los siguientes comandos prueban el prefijo personalizado:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="eaa1a-190">La [configuración predeterminada](#default) carga las variables de entorno y los argumentos de la línea de comandos con los prefijos `DOTNET_` y `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="eaa1a-191">ASP.NET Core usa los prefijos `DOTNET_` y `ASPNETCORE_` para la [configuración del host y la aplicación](xref:fundamentals/host/generic-host#host-configuration), pero no para la del usuario.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="eaa1a-192">Para obtener más información sobre la configuración del host y de la aplicación, consulte el artículo [Host genérico de .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="eaa1a-193">En [Azure App Service](https://azure.microsoft.com/services/app-service/), seleccione **Nueva configuración de la aplicación** en la página **Configuración > Configuración**.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="eaa1a-194">Los ajustes de configuración de Azure App Service:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="eaa1a-195">Se cifran en reposo y se transmiten a través de un canal cifrado.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="eaa1a-196">Se exponen como variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-196">Exposed as environment variables.</span></span>

<span data-ttu-id="eaa1a-197">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="eaa1a-198">Consulte la sección [Prefijos de cadena de conexión](#constr) para obtener información sobre las cadenas de conexión de base de datos de Azure.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="eaa1a-199">Línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-199">Command-line</span></span>

<span data-ttu-id="eaa1a-200">Si se usa la configuración [predeterminada](#default), <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de los pares de clave-valor de argumento de la línea de comandos después de los siguientes orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="eaa1a-201">Archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="eaa1a-202">[Secretos de aplicación (administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa1a-203">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-203">Environment variables.</span></span>

<span data-ttu-id="eaa1a-204">De [forma predeterminada](#default), los valores de configuración establecidos en la línea de comandos reemplazan los valores de configuración establecidos con el resto de proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="eaa1a-205">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-205">Command-line arguments</span></span>

<span data-ttu-id="eaa1a-206">El comando siguiente establece las claves y los valores mediante `=`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="eaa1a-207">El comando siguiente establece las claves y los valores mediante `/`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="eaa1a-208">El comando siguiente establece las claves y los valores mediante `--`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="eaa1a-209">El valor de la clave:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-209">The key value:</span></span>

* <span data-ttu-id="eaa1a-210">Debe seguir a un signo `=`, o bien la clave debe tener un prefijo `--` o `/` cuando el valor sigue a un espacio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="eaa1a-211">No es necesario si se usa `=`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="eaa1a-212">Por ejemplo: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="eaa1a-213">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan `=` con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="eaa1a-214">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="eaa1a-214">Switch mappings</span></span>

<span data-ttu-id="eaa1a-215">Las asignaciones de modificador admiten la lógica de sustitución de nombres de **clave**.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="eaa1a-216">Proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="eaa1a-217">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="eaa1a-218">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="eaa1a-219">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="eaa1a-220">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="eaa1a-221">Los modificadores deben comenzar con `-` o `--`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="eaa1a-222">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="eaa1a-223">Para usar un diccionario de asignaciones de modificador, páselo a la llamada a `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="eaa1a-224">El código siguiente muestra los valores de clave para las claves reemplazadas:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-225">Ejecute el siguiente comando para probar el reemplazo de claves:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="eaa1a-226">Nota: Actualmente, no se puede usar `=` para establecer los valores de reemplazo de clave con un solo guion `-`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="eaa1a-227">Consulte [este problema de GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="eaa1a-228">El siguiente comando sirve para probar el reemplazo de claves:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="eaa1a-229">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="eaa1a-230">En la llamada de `AddCommandLine` al método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa1a-231">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="eaa1a-232">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="eaa1a-232">Hierarchical configuration data</span></span>

<span data-ttu-id="eaa1a-233">La API de configuración lee los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="eaa1a-234">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="eaa1a-235">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las opciones de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-236">La mejor manera de leer datos de configuración jerárquica es usar el patrón de opciones.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="eaa1a-237">Para obtener más información, vea la sección [Enlace de datos de configuración jerárquica](#optpat) en este documento.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="eaa1a-238">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="eaa1a-239">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="eaa1a-240">Claves y valores de configuración</span><span class="sxs-lookup"><span data-stu-id="eaa1a-240">Configuration keys and values</span></span>

<span data-ttu-id="eaa1a-241">Las claves de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-241">Configuration keys:</span></span>

* <span data-ttu-id="eaa1a-242">No distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-242">Are case-insensitive.</span></span> <span data-ttu-id="eaa1a-243">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="eaa1a-244">Si se establece una clave y un valor en más de un proveedor de configuración, se usa el valor del último proveedor agregado.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="eaa1a-245">Para obtener más información, vea la sección [Configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="eaa1a-246">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="eaa1a-246">Hierarchical keys</span></span>
  * <span data-ttu-id="eaa1a-247">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="eaa1a-248">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="eaa1a-249">Todas las plataformas admiten un carácter de subrayado doble, `__`, que se convierte automáticamente en un signo de dos puntos `:`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="eaa1a-250">En Azure Key Vault, las claves jerárquicas usan `--` como separador.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="eaa1a-251">El [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) reemplaza automáticamente `--` con `:` cuando los secretos se cargan en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="eaa1a-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="eaa1a-253">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#boa).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="eaa1a-254">Los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-254">Configuration values:</span></span>

* <span data-ttu-id="eaa1a-255">Son cadenas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-255">Are strings.</span></span>
* <span data-ttu-id="eaa1a-256">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="eaa1a-257">Proveedores de configuración</span><span class="sxs-lookup"><span data-stu-id="eaa1a-257">Configuration providers</span></span>

<span data-ttu-id="eaa1a-258">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="eaa1a-259">Proveedor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-259">Provider</span></span> | <span data-ttu-id="eaa1a-260">Proporciona la configuración de</span><span class="sxs-lookup"><span data-stu-id="eaa1a-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="eaa1a-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-262">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-262">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-263">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-263">'Identity'</span></span>
- <span data-ttu-id="eaa1a-264">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-265">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-265">'Razor'</span></span>
- <span data-ttu-id="eaa1a-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-268">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-268">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-269">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-269">'Identity'</span></span>
- <span data-ttu-id="eaa1a-270">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-271">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-271">'Razor'</span></span>
- <span data-ttu-id="eaa1a-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-272">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-274">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-274">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-275">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-275">'Identity'</span></span>
- <span data-ttu-id="eaa1a-276">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-277">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-277">'Razor'</span></span>
- <span data-ttu-id="eaa1a-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-280">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-280">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-281">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-281">'Identity'</span></span>
- <span data-ttu-id="eaa1a-282">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-283">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-283">'Razor'</span></span>
- <span data-ttu-id="eaa1a-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-286">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-286">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-287">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-287">'Identity'</span></span>
- <span data-ttu-id="eaa1a-288">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-289">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-289">'Razor'</span></span>
- <span data-ttu-id="eaa1a-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-292">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-292">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-293">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-293">'Identity'</span></span>
- <span data-ttu-id="eaa1a-294">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-295">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-295">'Razor'</span></span>
- <span data-ttu-id="eaa1a-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-298">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-298">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-299">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-299">'Identity'</span></span>
- <span data-ttu-id="eaa1a-300">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-301">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-301">'Razor'</span></span>
- <span data-ttu-id="eaa1a-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-304">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-304">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-305">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-305">'Identity'</span></span>
- <span data-ttu-id="eaa1a-306">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-307">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-307">'Razor'</span></span>
- <span data-ttu-id="eaa1a-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-310">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-310">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-311">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-311">'Identity'</span></span>
- <span data-ttu-id="eaa1a-312">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-313">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-313">'Razor'</span></span>
- <span data-ttu-id="eaa1a-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-316">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-316">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-317">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-317">'Identity'</span></span>
- <span data-ttu-id="eaa1a-318">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-319">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-319">'Razor'</span></span>
- <span data-ttu-id="eaa1a-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-322">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-322">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-323">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-323">'Identity'</span></span>
- <span data-ttu-id="eaa1a-324">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-325">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-325">'Razor'</span></span>
- <span data-ttu-id="eaa1a-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-328">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-328">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-329">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-329">'Identity'</span></span>
- <span data-ttu-id="eaa1a-330">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-331">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-331">'Razor'</span></span>
- <span data-ttu-id="eaa1a-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-334">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-334">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-335">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-335">'Identity'</span></span>
- <span data-ttu-id="eaa1a-336">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-337">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-337">'Razor'</span></span>
- <span data-ttu-id="eaa1a-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-340">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-340">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-341">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-341">'Identity'</span></span>
- <span data-ttu-id="eaa1a-342">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-343">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-343">'Razor'</span></span>
- <span data-ttu-id="eaa1a-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-346">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-347">'Identity'</span></span>
- <span data-ttu-id="eaa1a-348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-349">'Razor'</span></span>
- <span data-ttu-id="eaa1a-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-352">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-353">'Identity'</span></span>
- <span data-ttu-id="eaa1a-354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-355">'Razor'</span></span>
- <span data-ttu-id="eaa1a-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-358">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-359">'Identity'</span></span>
- <span data-ttu-id="eaa1a-360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-361">'Razor'</span></span>
- <span data-ttu-id="eaa1a-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-362">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-363">------------------ | | [Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) | Azure Key Vault | | [Proveedor de configuración de aplicación de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Configuración de aplicación de Azure | | [Proveedor de configuración de la línea de comandos](#clcp) | Parámetros de la línea de comandos | | [Proveedor de configuración personalizada](#custom-configuration-provider) | Origen personalizado | | [Proveedor de configuración de variables de entorno ](#evcp) | Variables de entorno | | [Proveedor de configuración de archivo](#file-configuration-provider) | Archivos INI, JSON y XML | | [Proveedor de configuración de clave por archivo](#key-per-file-configuration-provider) | Archivos de directorio | | [Proveedor de configuración de memoria ](#memory-configuration-provider) | Colecciones en memoria || [Administrador de secretos](xref:security/app-secrets) | Archivo en el directorio del perfil de usuario |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="eaa1a-364">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="eaa1a-365">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="eaa1a-366">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="eaa1a-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="eaa1a-367">*appsettings.json*</span></span>
1. <span data-ttu-id="eaa1a-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="eaa1a-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="eaa1a-369">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="eaa1a-370">Variables de entorno con el [proveedor de configuración de variables de entorno](#evcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="eaa1a-371">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="eaa1a-372">Una práctica común es agregar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="eaa1a-373">La secuencia de proveedores anterior se usa en la [configuración predeterminada](#default).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="eaa1a-374">Prefijos de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="eaa1a-374">Connection string prefixes</span></span>

<span data-ttu-id="eaa1a-375">La API de configuración tiene reglas de procesamiento especiales para cuatro variables de entorno de cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="eaa1a-376">Estas cadenas de conexión están implicadas en la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="eaa1a-377">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación con la [configuración predeterminada](#default) o cuando no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="eaa1a-378">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="eaa1a-378">Connection string prefix</span></span> | <span data-ttu-id="eaa1a-379">Proveedor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-379">Provider</span></span> |
| ---
<span data-ttu-id="eaa1a-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-381">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-381">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-382">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-382">'Identity'</span></span>
- <span data-ttu-id="eaa1a-383">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-384">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-384">'Razor'</span></span>
- <span data-ttu-id="eaa1a-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-387">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-387">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-388">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-388">'Identity'</span></span>
- <span data-ttu-id="eaa1a-389">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-390">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-390">'Razor'</span></span>
- <span data-ttu-id="eaa1a-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-393">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-393">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-394">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-394">'Identity'</span></span>
- <span data-ttu-id="eaa1a-395">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-396">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-396">'Razor'</span></span>
- <span data-ttu-id="eaa1a-397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-399">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-399">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-400">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-400">'Identity'</span></span>
- <span data-ttu-id="eaa1a-401">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-402">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-402">'Razor'</span></span>
- <span data-ttu-id="eaa1a-403">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-405">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-405">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-406">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-406">'Identity'</span></span>
- <span data-ttu-id="eaa1a-407">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-408">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-408">'Razor'</span></span>
- <span data-ttu-id="eaa1a-409">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-411">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-411">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-412">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-412">'Identity'</span></span>
- <span data-ttu-id="eaa1a-413">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-414">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-414">'Razor'</span></span>
- <span data-ttu-id="eaa1a-415">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-417">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-417">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-418">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-418">'Identity'</span></span>
- <span data-ttu-id="eaa1a-419">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-420">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-420">'Razor'</span></span>
- <span data-ttu-id="eaa1a-421">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-423">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-423">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-424">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-424">'Identity'</span></span>
- <span data-ttu-id="eaa1a-425">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-426">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-426">'Razor'</span></span>
- <span data-ttu-id="eaa1a-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-429">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-429">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-430">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-430">'Identity'</span></span>
- <span data-ttu-id="eaa1a-431">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-432">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-432">'Razor'</span></span>
- <span data-ttu-id="eaa1a-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-435">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-435">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-436">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-436">'Identity'</span></span>
- <span data-ttu-id="eaa1a-437">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-438">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-438">'Razor'</span></span>
- <span data-ttu-id="eaa1a-439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-439">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-441">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-441">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-442">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-442">'Identity'</span></span>
- <span data-ttu-id="eaa1a-443">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-444">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-444">'Razor'</span></span>
- <span data-ttu-id="eaa1a-445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-447">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-447">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-448">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-448">'Identity'</span></span>
- <span data-ttu-id="eaa1a-449">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-450">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-450">'Razor'</span></span>
- <span data-ttu-id="eaa1a-451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-451">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-452">---- | | `CUSTOMCONNSTR_` | Proveedor personalizado | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="eaa1a-453">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="eaa1a-454">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="eaa1a-455">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="eaa1a-456">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-456">Environment variable key</span></span> | <span data-ttu-id="eaa1a-457">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="eaa1a-457">Converted configuration key</span></span> | <span data-ttu-id="eaa1a-458">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="eaa1a-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-460">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-460">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-461">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-461">'Identity'</span></span>
- <span data-ttu-id="eaa1a-462">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-463">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-463">'Razor'</span></span>
- <span data-ttu-id="eaa1a-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-466">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-466">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-467">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-467">'Identity'</span></span>
- <span data-ttu-id="eaa1a-468">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-469">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-469">'Razor'</span></span>
- <span data-ttu-id="eaa1a-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-472">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-472">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-473">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-473">'Identity'</span></span>
- <span data-ttu-id="eaa1a-474">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-475">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-475">'Razor'</span></span>
- <span data-ttu-id="eaa1a-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-478">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-478">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-479">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-479">'Identity'</span></span>
- <span data-ttu-id="eaa1a-480">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-481">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-481">'Razor'</span></span>
- <span data-ttu-id="eaa1a-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-484">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-484">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-485">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-485">'Identity'</span></span>
- <span data-ttu-id="eaa1a-486">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-487">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-487">'Razor'</span></span>
- <span data-ttu-id="eaa1a-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-490">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-490">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-491">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-491">'Identity'</span></span>
- <span data-ttu-id="eaa1a-492">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-493">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-493">'Razor'</span></span>
- <span data-ttu-id="eaa1a-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-496">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-496">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-497">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-497">'Identity'</span></span>
- <span data-ttu-id="eaa1a-498">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-499">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-499">'Razor'</span></span>
- <span data-ttu-id="eaa1a-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-502">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-502">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-503">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-503">'Identity'</span></span>
- <span data-ttu-id="eaa1a-504">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-505">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-505">'Razor'</span></span>
- <span data-ttu-id="eaa1a-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-508">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-508">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-509">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-509">'Identity'</span></span>
- <span data-ttu-id="eaa1a-510">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-511">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-511">'Razor'</span></span>
- <span data-ttu-id="eaa1a-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-514">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-514">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-515">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-515">'Identity'</span></span>
- <span data-ttu-id="eaa1a-516">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-517">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-517">'Razor'</span></span>
- <span data-ttu-id="eaa1a-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-518">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-520">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-520">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-521">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-521">'Identity'</span></span>
- <span data-ttu-id="eaa1a-522">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-523">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-523">'Razor'</span></span>
- <span data-ttu-id="eaa1a-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-526">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-526">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-527">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-527">'Identity'</span></span>
- <span data-ttu-id="eaa1a-528">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-529">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-529">'Razor'</span></span>
- <span data-ttu-id="eaa1a-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-532">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-532">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-533">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-533">'Identity'</span></span>
- <span data-ttu-id="eaa1a-534">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-535">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-535">'Razor'</span></span>
- <span data-ttu-id="eaa1a-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-538">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-538">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-539">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-539">'Identity'</span></span>
- <span data-ttu-id="eaa1a-540">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-541">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-541">'Razor'</span></span>
- <span data-ttu-id="eaa1a-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-544">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-544">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-545">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-545">'Identity'</span></span>
- <span data-ttu-id="eaa1a-546">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-547">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-547">'Razor'</span></span>
- <span data-ttu-id="eaa1a-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-550">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-550">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-551">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-551">'Identity'</span></span>
- <span data-ttu-id="eaa1a-552">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-553">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-553">'Razor'</span></span>
- <span data-ttu-id="eaa1a-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-556">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-556">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-557">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-557">'Identity'</span></span>
- <span data-ttu-id="eaa1a-558">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-559">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-559">'Razor'</span></span>
- <span data-ttu-id="eaa1a-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-562">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-562">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-563">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-563">'Identity'</span></span>
- <span data-ttu-id="eaa1a-564">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-565">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-565">'Razor'</span></span>
- <span data-ttu-id="eaa1a-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-568">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-568">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-569">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-569">'Identity'</span></span>
- <span data-ttu-id="eaa1a-570">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-571">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-571">'Razor'</span></span>
- <span data-ttu-id="eaa1a-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-574">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-574">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-575">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-575">'Identity'</span></span>
- <span data-ttu-id="eaa1a-576">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-577">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-577">'Razor'</span></span>
- <span data-ttu-id="eaa1a-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-580">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-580">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-581">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-581">'Identity'</span></span>
- <span data-ttu-id="eaa1a-582">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-583">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-583">'Razor'</span></span>
- <span data-ttu-id="eaa1a-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-584">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-586">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-586">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-587">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-587">'Identity'</span></span>
- <span data-ttu-id="eaa1a-588">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-589">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-589">'Razor'</span></span>
- <span data-ttu-id="eaa1a-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-592">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-592">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-593">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-593">'Identity'</span></span>
- <span data-ttu-id="eaa1a-594">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-595">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-595">'Razor'</span></span>
- <span data-ttu-id="eaa1a-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-598">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-598">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-599">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-599">'Identity'</span></span>
- <span data-ttu-id="eaa1a-600">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-601">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-601">'Razor'</span></span>
- <span data-ttu-id="eaa1a-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-604">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-604">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-605">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-605">'Identity'</span></span>
- <span data-ttu-id="eaa1a-606">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-607">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-607">'Razor'</span></span>
- <span data-ttu-id="eaa1a-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-610">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-610">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-611">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-611">'Identity'</span></span>
- <span data-ttu-id="eaa1a-612">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-613">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-613">'Razor'</span></span>
- <span data-ttu-id="eaa1a-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-616">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-616">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-617">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-617">'Identity'</span></span>
- <span data-ttu-id="eaa1a-618">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-619">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-619">'Razor'</span></span>
- <span data-ttu-id="eaa1a-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-622">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-622">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-623">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-623">'Identity'</span></span>
- <span data-ttu-id="eaa1a-624">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-625">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-625">'Razor'</span></span>
- <span data-ttu-id="eaa1a-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-628">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-628">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-629">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-629">'Identity'</span></span>
- <span data-ttu-id="eaa1a-630">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-631">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-631">'Razor'</span></span>
- <span data-ttu-id="eaa1a-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-634">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-634">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-635">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-635">'Identity'</span></span>
- <span data-ttu-id="eaa1a-636">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-637">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-637">'Razor'</span></span>
- <span data-ttu-id="eaa1a-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-640">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-640">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-641">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-641">'Identity'</span></span>
- <span data-ttu-id="eaa1a-642">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-643">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-643">'Razor'</span></span>
- <span data-ttu-id="eaa1a-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-646">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-646">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-647">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-647">'Identity'</span></span>
- <span data-ttu-id="eaa1a-648">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-649">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-649">'Razor'</span></span>
- <span data-ttu-id="eaa1a-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-652">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-652">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-653">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-653">'Identity'</span></span>
- <span data-ttu-id="eaa1a-654">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-655">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-655">'Razor'</span></span>
- <span data-ttu-id="eaa1a-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-658">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-658">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-659">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-659">'Identity'</span></span>
- <span data-ttu-id="eaa1a-660">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-661">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-661">'Razor'</span></span>
- <span data-ttu-id="eaa1a-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-664">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-664">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-665">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-665">'Identity'</span></span>
- <span data-ttu-id="eaa1a-666">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-667">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-667">'Razor'</span></span>
- <span data-ttu-id="eaa1a-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-670">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-670">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-671">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-671">'Identity'</span></span>
- <span data-ttu-id="eaa1a-672">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-673">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-673">'Razor'</span></span>
- <span data-ttu-id="eaa1a-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-676">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-676">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-677">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-677">'Identity'</span></span>
- <span data-ttu-id="eaa1a-678">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-679">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-679">'Razor'</span></span>
- <span data-ttu-id="eaa1a-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-682">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-682">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-683">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-683">'Identity'</span></span>
- <span data-ttu-id="eaa1a-684">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-685">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-685">'Razor'</span></span>
- <span data-ttu-id="eaa1a-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-688">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-688">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-689">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-689">'Identity'</span></span>
- <span data-ttu-id="eaa1a-690">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-691">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-691">'Razor'</span></span>
- <span data-ttu-id="eaa1a-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-694">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-694">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-695">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-695">'Identity'</span></span>
- <span data-ttu-id="eaa1a-696">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-697">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-697">'Razor'</span></span>
- <span data-ttu-id="eaa1a-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-700">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-700">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-701">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-701">'Identity'</span></span>
- <span data-ttu-id="eaa1a-702">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-703">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-703">'Razor'</span></span>
- <span data-ttu-id="eaa1a-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-706">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-706">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-707">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-707">'Identity'</span></span>
- <span data-ttu-id="eaa1a-708">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-709">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-709">'Razor'</span></span>
- <span data-ttu-id="eaa1a-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-712">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-712">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-713">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-713">'Identity'</span></span>
- <span data-ttu-id="eaa1a-714">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-715">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-715">'Razor'</span></span>
- <span data-ttu-id="eaa1a-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-718">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-718">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-719">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-719">'Identity'</span></span>
- <span data-ttu-id="eaa1a-720">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-721">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-721">'Razor'</span></span>
- <span data-ttu-id="eaa1a-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-724">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-724">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-725">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-725">'Identity'</span></span>
- <span data-ttu-id="eaa1a-726">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-727">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-727">'Razor'</span></span>
- <span data-ttu-id="eaa1a-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-730">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-730">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-731">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-731">'Identity'</span></span>
- <span data-ttu-id="eaa1a-732">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-733">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-733">'Razor'</span></span>
- <span data-ttu-id="eaa1a-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-736">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-736">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-737">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-737">'Identity'</span></span>
- <span data-ttu-id="eaa1a-738">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-739">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-739">'Razor'</span></span>
- <span data-ttu-id="eaa1a-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-742">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-742">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-743">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-743">'Identity'</span></span>
- <span data-ttu-id="eaa1a-744">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-745">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-745">'Razor'</span></span>
- <span data-ttu-id="eaa1a-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-748">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-748">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-749">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-749">'Identity'</span></span>
- <span data-ttu-id="eaa1a-750">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-751">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-751">'Razor'</span></span>
- <span data-ttu-id="eaa1a-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-754">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-754">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-755">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-755">'Identity'</span></span>
- <span data-ttu-id="eaa1a-756">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-757">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-757">'Razor'</span></span>
- <span data-ttu-id="eaa1a-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-760">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-760">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-761">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-761">'Identity'</span></span>
- <span data-ttu-id="eaa1a-762">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-763">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-763">'Razor'</span></span>
- <span data-ttu-id="eaa1a-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-766">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-766">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-767">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-767">'Identity'</span></span>
- <span data-ttu-id="eaa1a-768">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-769">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-769">'Razor'</span></span>
- <span data-ttu-id="eaa1a-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-772">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-772">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-773">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-773">'Identity'</span></span>
- <span data-ttu-id="eaa1a-774">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-775">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-775">'Razor'</span></span>
- <span data-ttu-id="eaa1a-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-778">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-778">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-779">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-779">'Identity'</span></span>
- <span data-ttu-id="eaa1a-780">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-781">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-781">'Razor'</span></span>
- <span data-ttu-id="eaa1a-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-784">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-784">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-785">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-785">'Identity'</span></span>
- <span data-ttu-id="eaa1a-786">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-787">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-787">'Razor'</span></span>
- <span data-ttu-id="eaa1a-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-790">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-790">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-791">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-791">'Identity'</span></span>
- <span data-ttu-id="eaa1a-792">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-793">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-793">'Razor'</span></span>
- <span data-ttu-id="eaa1a-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-796">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-796">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-797">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-797">'Identity'</span></span>
- <span data-ttu-id="eaa1a-798">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-799">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-799">'Razor'</span></span>
- <span data-ttu-id="eaa1a-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-802">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-802">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-803">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-803">'Identity'</span></span>
- <span data-ttu-id="eaa1a-804">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-805">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-805">'Razor'</span></span>
- <span data-ttu-id="eaa1a-806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-806">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="eaa1a-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="eaa1a-809">Valor: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="eaa1a-810">Valor: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="eaa1a-811">Valor: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="eaa1a-812">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="eaa1a-812">JSON configuration provider</span></span>

<span data-ttu-id="eaa1a-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="eaa1a-814">Las sobrecargas pueden especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-814">Overloads can specify:</span></span>

* <span data-ttu-id="eaa1a-815">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-815">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa1a-816">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="eaa1a-817">Observe el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="eaa1a-818">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-818">The preceding code:</span></span>

* <span data-ttu-id="eaa1a-819">Configura el proveedor de configuración JSON para que cargue el archivo *MyConfig.json* con las siguientes opciones:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="eaa1a-820">`optional: true`: el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="eaa1a-821">`reloadOnChange: true`: el archivo se recarga cuando se guardan los cambios.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="eaa1a-822">Lee los [proveedores de configuración predeterminados](#default) antes que el archivo *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="eaa1a-823">La configuración del archivo *MyConfig.json* invalida la de los proveedores de configuración predeterminados, incluidos el [proveedor de configuración de variables de entorno](#evcp) y el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="eaa1a-824">Normalmente ***no*** querrá que un archivo JSON personalizado invalide los valores establecidos en el [proveedor de configuración de variables de entorno](#evcp) ni en el [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="eaa1a-825">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="eaa1a-826">En el código anterior, la configuración de los archivos *MyConfig.json* y *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="eaa1a-827">Invalida la configuración de los archivos *appsettings.json* y *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="eaa1a-828">Es reemplazada por la configuración del [proveedor de configuración de variables de entorno](#evcp) y del [proveedor de configuración de línea de comandos](#clcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="eaa1a-829">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="eaa1a-830">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="eaa1a-831">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-831">File configuration provider</span></span>

<span data-ttu-id="eaa1a-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="eaa1a-833">Los siguientes proveedores de configuración se derivan de `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="eaa1a-834">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="eaa1a-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="eaa1a-835">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="eaa1a-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="eaa1a-836">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="eaa1a-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="eaa1a-837">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="eaa1a-837">INI configuration provider</span></span>

<span data-ttu-id="eaa1a-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa1a-839">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="eaa1a-840">En el código anterior, se reemplaza la configuración de los archivos *MyIniConfig.ini* y *MyIniConfig*.`Environment`.*ini* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="eaa1a-841">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="eaa1a-842">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="eaa1a-843">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="eaa1a-844">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="eaa1a-845">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="eaa1a-845">XML configuration provider</span></span>

<span data-ttu-id="eaa1a-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa1a-847">El código siguiente borra todos los proveedores de configuración y agrega varios:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="eaa1a-848">En el código anterior, se reemplaza la configuración de los archivos *MyXMLFile.xml* y *MyXMLFile*.`Environment`.*xml* por la configuración de:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="eaa1a-849">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="eaa1a-850">[Proveedor de configuración de línea de comandos](#clcp)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="eaa1a-851">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contiene el siguiente archivo *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="eaa1a-852">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra algunas de las configuraciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-853">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="eaa1a-854">El código siguiente lee el archivo de configuración anterior y muestra las claves y los valores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-855">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="eaa1a-856">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa1a-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="eaa1a-857">key:attribute</span></span>
* <span data-ttu-id="eaa1a-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="eaa1a-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="eaa1a-859">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="eaa1a-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="eaa1a-861">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-861">The key is the file name.</span></span> <span data-ttu-id="eaa1a-862">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-862">The value contains the file's contents.</span></span> <span data-ttu-id="eaa1a-863">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="eaa1a-864">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="eaa1a-865">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="eaa1a-866">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa1a-867">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="eaa1a-868">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="eaa1a-869">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="eaa1a-870">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="eaa1a-871">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="eaa1a-872">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="eaa1a-872">Memory configuration provider</span></span>

<span data-ttu-id="eaa1a-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="eaa1a-874">El código siguiente agrega una colección en memoria al sistema de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="eaa1a-875">El siguiente código de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) muestra las opciones de configuración anteriores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-876">En el código anterior, `config.AddInMemoryCollection(Dict)` se agrega después de los [proveedores de configuración predeterminados](#default).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="eaa1a-877">Para obtener un ejemplo de ordenación de los proveedores de configuración, consulte la sección [Proveedor de configuración JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="eaa1a-878">Consulte la sección [Enlace de matrices](#boa) para ver otro ejemplo que usa `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="eaa1a-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="eaa1a-879">GetValue</span></span>

<span data-ttu-id="eaa1a-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-881">En el código anterior, si `NumberKey` no se encuentra en la configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="eaa1a-882">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="eaa1a-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="eaa1a-883">Para los ejemplos que aparecen a continuación, considere el siguiente archivo *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="eaa1a-884">El código siguiente agrega *MySubsection.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="eaa1a-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="eaa1a-885">GetSection</span></span>

<span data-ttu-id="eaa1a-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) devuelve una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="eaa1a-887">El código siguiente devuelve los valores de `section1`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-888">El código siguiente devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-889">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="eaa1a-890">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="eaa1a-891">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="eaa1a-892">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="eaa1a-893">GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="eaa1a-893">GetChildren and Exists</span></span>

<span data-ttu-id="eaa1a-894">El código siguiente llama a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) y devuelve los valores de `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-895">El código anterior llama a [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para comprobar que la sección existe:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="eaa1a-896">Enlace de matrices</span><span class="sxs-lookup"><span data-stu-id="eaa1a-896">Bind an array</span></span>

<span data-ttu-id="eaa1a-897">El método [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) permite enlazar matrices a objetos mediante el uso de los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="eaa1a-898">Cualquier formato de matriz que exponga un segmento de clave numérica es capaz de enlazar una matriz a una matriz de clase [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="eaa1a-899">Tenga en cuenta este archivo *MyArray.json* de la [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="eaa1a-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="eaa1a-900">El código siguiente agrega *MyArray.json* a los proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="eaa1a-901">El código siguiente lee la configuración y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-902">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="eaa1a-903">En el resultado anterior, el índice 3 tiene el valor `value40`, que corresponde a `"4": "value40",` en *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="eaa1a-904">Los índices de matriz enlazados son continuos y no están enlazados al índice de la clave de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="eaa1a-905">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en objetos enlazados.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="eaa1a-906">El código siguiente carga la configuración de `array:entries` con el método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="eaa1a-907">El código siguiente lee la configuración de `arrayDict` `Dictionary` y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-908">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="eaa1a-909">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="eaa1a-910">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="eaa1a-911">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="eaa1a-912">Cualquier proveedor de configuración que lea el par clave-valor del índice &num;3 puede proporcionar el elemento de configuración omitido para el índice &num;3 antes del enlace a la instancia `ArrayExample`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="eaa1a-913">Fíjese en el siguiente archivo *Value3.json* de la descarga de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="eaa1a-914">En el código siguiente se incluye la configuración para *Value3.json* y `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="eaa1a-915">El código siguiente lee la configuración anterior y muestra los valores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-916">El código anterior devuelve el siguiente resultado:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="eaa1a-917">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="eaa1a-918">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="eaa1a-918">Custom configuration provider</span></span>

<span data-ttu-id="eaa1a-919">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="eaa1a-920">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="eaa1a-921">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="eaa1a-922">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="eaa1a-923">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="eaa1a-924">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="eaa1a-925">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="eaa1a-926">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="eaa1a-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="eaa1a-928">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="eaa1a-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="eaa1a-930">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="eaa1a-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="eaa1a-932">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="eaa1a-933">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="eaa1a-934">Puesto que las [claves de configuración no distinguen entre mayúsculas y minúsculas](#keys), el diccionario empleado para iniciar la base de datos se crea con el comparador que tampoco hace tal distinción ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="eaa1a-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="eaa1a-936">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="eaa1a-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="eaa1a-938">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="eaa1a-939">Acceso a la configuración en métodos Startup</span><span class="sxs-lookup"><span data-stu-id="eaa1a-939">Access configuration in Startup</span></span>

<span data-ttu-id="eaa1a-940">En el código siguiente se muestran los datos de configuración de los métodos `Startup`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="eaa1a-941">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="eaa1a-942">Acceso a la configuración en Razor Pages</span><span class="sxs-lookup"><span data-stu-id="eaa1a-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="eaa1a-943">En el código siguiente se muestran los datos de configuración en una página de RazorPages:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="eaa1a-944">En el siguiente código se agrega `MyOptions` al contenedor de servicios con <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> y se enlaza a la configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="eaa1a-945">En el marcado siguiente se usa la directiva [`@inject`](xref:mvc/views/razor#inject) Razor para resolver y mostrar los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="eaa1a-946">Acceso a la configuración en un archivo de vista de MVC</span><span class="sxs-lookup"><span data-stu-id="eaa1a-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="eaa1a-947">En el código siguiente se muestran los datos de configuración de una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="eaa1a-948">Configurar opciones con un delegado</span><span class="sxs-lookup"><span data-stu-id="eaa1a-948">Configure options with a delegate</span></span>

<span data-ttu-id="eaa1a-949">Las opciones configuradas en un delegado invalidan los valores establecidos en los proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="eaa1a-950">La configuración de opciones con un delegado se muestra como ejemplo 2 en la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="eaa1a-951">En el código siguiente, se agrega un servicio <xref:Microsoft.Extensions.Options.IConfigureOptions%601> al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="eaa1a-952">Usa un delegado para configurar los valores de `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="eaa1a-953">En el código siguiente se muestran los valores de las opciones:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="eaa1a-954">En el ejemplo anterior, los valores de `Option1` y `Option2` se especifican en *appSettings.json* y, luego, se reemplazan por el delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="eaa1a-955">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="eaa1a-955">Host versus app configuration</span></span>

<span data-ttu-id="eaa1a-956">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="eaa1a-957">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="eaa1a-958">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="eaa1a-959">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="eaa1a-960">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="eaa1a-961">Configuración predeterminada del host</span><span class="sxs-lookup"><span data-stu-id="eaa1a-961">Default host configuration</span></span>

<span data-ttu-id="eaa1a-962">Para obtener más información sobre la configuración predeterminada al usar el [host de web](xref:fundamentals/host/web-host), vea la [versión de este tema para ASP.NET Core 2.2](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="eaa1a-963">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="eaa1a-964">Las variables de entorno con el prefijo `DOTNET_` (por ejemplo, `DOTNET_ENVIRONMENT`) mediante el [proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="eaa1a-965">El prefijo (`DOTNET_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="eaa1a-966">Argumentos de la línea de comandos con el [proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="eaa1a-967">La configuración predeterminada del host de web se ha establecido (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="eaa1a-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="eaa1a-968">Kestrel se usa como el servidor web y se ha configurado mediante los proveedores de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="eaa1a-969">Agregar el middleware de filtrado de host</span><span class="sxs-lookup"><span data-stu-id="eaa1a-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="eaa1a-970">Agregue el middleware de encabezados reenviados si la variable de entorno `ASPNETCORE_FORWARDEDHEADERS_ENABLED` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="eaa1a-971">Habilite la integración con IIS.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="eaa1a-972">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="eaa1a-972">Other configuration</span></span>

<span data-ttu-id="eaa1a-973">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="eaa1a-974">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="eaa1a-975">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="eaa1a-976">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="eaa1a-977">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="eaa1a-978">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="eaa1a-979">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="eaa1a-980">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="eaa1a-981">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="eaa1a-982">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eaa1a-983">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="eaa1a-983">Additional resources</span></span>

* [<span data-ttu-id="eaa1a-984">Configuración del código fuente</span><span class="sxs-lookup"><span data-stu-id="eaa1a-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa1a-985">La configuración de la aplicación en ASP.NET Core se basa en pares clave-valor establecidos por *proveedores de configuración*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="eaa1a-986">Los proveedores de configuración leen los datos de configuración en los pares clave-valor de distintos orígenes de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="eaa1a-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="eaa1a-987">Azure Key Vault</span></span>
* <span data-ttu-id="eaa1a-988">Configuración de aplicaciones de Azure</span><span class="sxs-lookup"><span data-stu-id="eaa1a-988">Azure App Configuration</span></span>
* <span data-ttu-id="eaa1a-989">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-989">Command-line arguments</span></span>
* <span data-ttu-id="eaa1a-990">Proveedores personalizados (instalados o creados)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="eaa1a-991">Archivos de directorio</span><span class="sxs-lookup"><span data-stu-id="eaa1a-991">Directory files</span></span>
* <span data-ttu-id="eaa1a-992">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-992">Environment variables</span></span>
* <span data-ttu-id="eaa1a-993">Objetos de .NET en memoria</span><span class="sxs-lookup"><span data-stu-id="eaa1a-993">In-memory .NET objects</span></span>
* <span data-ttu-id="eaa1a-994">Archivos de configuración</span><span class="sxs-lookup"><span data-stu-id="eaa1a-994">Settings files</span></span>

<span data-ttu-id="eaa1a-995">Los paquetes de configuración para escenarios de proveedor de configuración común ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) se incluyen en el [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="eaa1a-996">Los ejemplos de código que siguen y en la aplicación de ejemplo utilizan el espacio de nombres <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="eaa1a-997">El *patrón de opciones* es una extensión de los conceptos de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="eaa1a-998">Las opciones usan clases para representar grupos de configuraciones relacionadas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="eaa1a-999">Para obtener más información, vea <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="eaa1a-1000">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="eaa1a-1001">Configuración de host y de aplicación</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1001">Host versus app configuration</span></span>

<span data-ttu-id="eaa1a-1002">Antes de configurar e iniciar la aplicación, se configura e inicia un *host*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="eaa1a-1003">El host es responsable de la administración del inicio y la duración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="eaa1a-1004">Tanto la aplicación como el host se configuran mediante los proveedores de configuración que se describen en este tema.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="eaa1a-1005">Los pares clave-valor de configuración del host también se incluyen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="eaa1a-1006">Para más información sobre cómo se usan los proveedores de configuración cuando se compila el host y cómo afectan los orígenes de configuración a la configuración del host, consulte <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="eaa1a-1007">Otra configuración</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1007">Other configuration</span></span>

<span data-ttu-id="eaa1a-1008">Este tema solo concierne a la *configuración de aplicaciones*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="eaa1a-1009">Otros aspectos de la ejecución y el hospedaje de aplicaciones ASP.NET Core se configuran mediante archivos de configuración que no se describen en este tema:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="eaa1a-1010">*launch.json*/*launchSettings.json* son archivos de configuración de herramientas para el entorno de desarrollo, que se describe a continuación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="eaa1a-1011">En <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="eaa1a-1012">En el conjunto de documentación en el que se usan los archivos para configurar aplicaciones ASP.NET Core para escenarios de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="eaa1a-1013">*web.config* es un archivo de configuración del servidor, que se describe en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="eaa1a-1014">Para más información sobre cómo migrar la configuración de aplicaciones de versiones anteriores de ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="eaa1a-1015">Configuración predeterminada</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1015">Default configuration</span></span>

<span data-ttu-id="eaa1a-1016">Las aplicaciones web basadas en las plantillas de [dotnet new](/dotnet/core/tools/dotnet-new) de ASP.NET Core llaman a <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> al crear un host.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="eaa1a-1017">`CreateDefaultBuilder` proporciona la configuración predeterminada para la aplicación en el orden siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="eaa1a-1018">El contenido siguiente es válido para las aplicaciones que usen el [host de web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="eaa1a-1019">Para obtener más información sobre la configuración predeterminada al usar el [host genérico](xref:fundamentals/host/generic-host), vea la [versión más reciente de ese tema](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="eaa1a-1020">La configuración del host la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="eaa1a-1021">Variables de entorno prefijadas con `ASPNETCORE_` (por ejemplo, `ASPNETCORE_ENVIRONMENT`) con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="eaa1a-1022">El prefijo (`ASPNETCORE_`) se quita cuando se cargan los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="eaa1a-1023">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="eaa1a-1024">La configuración de la aplicación la proporcionan los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="eaa1a-1025">*appsettings.json* con el [Proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="eaa1a-1026">*appsettings.{Entorno}.json* con el [Proveedor de configuración de archivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="eaa1a-1027">[Administrador de secretos](xref:security/app-secrets) cuando la aplicación se ejecuta en el entorno `Development` por medio del ensamblado de entrada.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="eaa1a-1028">Variables de entorno con el [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="eaa1a-1029">Argumentos de la línea de comandos con el [Proveedor de configuración de línea de comandos](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="eaa1a-1030">Seguridad</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1030">Security</span></span>

<span data-ttu-id="eaa1a-1031">Adopte los procedimientos siguientes para proteger los datos de configuración confidenciales:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="eaa1a-1032">Nunca almacene contraseñas u otros datos confidenciales en el código del proveedor de configuración o en archivos de configuración de texto sin formato.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="eaa1a-1033">No use secretos de producción en los entornos de desarrollo o pruebas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="eaa1a-1034">Especifique los secretos fuera del proyecto para que no se confirmen en un repositorio de código fuente de manera accidental.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="eaa1a-1035">Para obtener más información, vea los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="eaa1a-1036"><xref:security/app-secrets>: incluye recomendaciones sobre el uso de variables de entorno para almacenar información confidencial.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="eaa1a-1037">El Administrador de secretos usa el proveedor de configuración de archivo para almacenar secretos de usuario en un archivo JSON del sistema local.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="eaa1a-1038">El proveedor de configuración de archivo se describe más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="eaa1a-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) almacena de forma segura secretos de aplicación para aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="eaa1a-1040">Para obtener más información, vea <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="eaa1a-1041">Datos de configuración jerárquica</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="eaa1a-1042">La API de configuración es capaz de mantener los datos de configuración jerárquica al disminuir los datos jerárquicos mediante el uso de un delimitador en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="eaa1a-1043">En el siguiente archivo JSON, hay cuatro claves en una estructura jerárquica de dos secciones:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="eaa1a-1044">Cuando el archivo se lee en la configuración, se crean claves únicas para mantener la estructura de datos jerárquicos original del origen de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="eaa1a-1045">Las secciones y claves se disminuyen con el uso de dos puntos (`:`) para mantener la estructura original:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="eaa1a-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1046">section0:key0</span></span>
* <span data-ttu-id="eaa1a-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1047">section0:key1</span></span>
* <span data-ttu-id="eaa1a-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1048">section1:key0</span></span>
* <span data-ttu-id="eaa1a-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1049">section1:key1</span></span>

<span data-ttu-id="eaa1a-1050">Los métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> y <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> están disponibles para aislar las secciones y los elementos secundarios de una sección en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="eaa1a-1051">Estos métodos se describen más adelante en la sección [GetSection, GetChildren y Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="eaa1a-1052">Convenciones</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="eaa1a-1053">Orígenes y proveedores</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1053">Sources and providers</span></span>

<span data-ttu-id="eaa1a-1054">Al iniciar la aplicación, los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="eaa1a-1055">Los proveedores de configuración que implementan la detección de cambios tienen la capacidad de volver a cargar la configuración cuando se cambia una configuración subyacente.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="eaa1a-1056">Por ejemplo, el proveedor de configuración de archivos (descrito más adelante en este tema) y el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) implementan la detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="eaa1a-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponible en el contenedor de [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="eaa1a-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> se puede insertar en Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> o MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obtener la configuración de la clase.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="eaa1a-1059">En los ejemplos siguientes, se usa el campo `_config` para tener acceso a los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="eaa1a-1060">Los proveedores de configuración no pueden usar la inserción de dependencias, porque no está disponible cuando el host los configura.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="eaa1a-1061">Teclas</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1061">Keys</span></span>

<span data-ttu-id="eaa1a-1062">Las claves de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="eaa1a-1063">Las claves no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="eaa1a-1064">Por ejemplo, `ConnectionString` y `connectionstring` se consideran claves equivalente.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="eaa1a-1065">Si los mismos o distintos proveedores de configuración establecen un valor para la misma clave, el último valor establecido en la clave es el valor que se usa.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="eaa1a-1066">Claves jerárquicas</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="eaa1a-1067">Dentro de la API de configuración, un separador de dos puntos (`:`) funciona en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="eaa1a-1068">En las variables de entorno, puede que un separador de dos puntos no funcione en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="eaa1a-1069">Todas las plataformas admiten un carácter de subrayado doble (`__`), que se convierte automáticamente en un signo de dos puntos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="eaa1a-1070">En Azure Key Vault, las claves jerárquicas usan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="eaa1a-1071">Escriba código para reemplazar los guiones por dos puntos cuando los secretos se carguen en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="eaa1a-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="eaa1a-1073">El enlace de matriz se describe en la sección [Enlace de una matriz a una clase](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="eaa1a-1074">Valores</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1074">Values</span></span>

<span data-ttu-id="eaa1a-1075">Los valores de configuración adoptan las convenciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="eaa1a-1076">Los valores son cadenas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1076">Values are strings.</span></span>
* <span data-ttu-id="eaa1a-1077">Los valores NULL no se pueden almacenar en la configuración ni enlazar a los objetos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="eaa1a-1078">Proveedores</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1078">Providers</span></span>

<span data-ttu-id="eaa1a-1079">La siguiente tabla muestra los proveedores de configuración disponibles para las aplicaciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="eaa1a-1080">Proveedor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1080">Provider</span></span> | <span data-ttu-id="eaa1a-1081">Proporciona la configuración de&hellip;</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="eaa1a-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1083">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1083">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1084">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1084">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1085">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1086">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1086">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1089">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1089">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1090">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1090">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1091">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1092">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1092">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1095">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1095">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1096">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1096">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1097">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1098">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1098">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1101">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1101">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1102">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1102">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1103">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1104">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1104">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1107">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1107">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1108">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1108">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1109">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1110">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1110">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1113">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1113">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1114">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1114">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1115">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1116">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1116">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1119">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1119">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1120">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1120">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1121">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1122">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1122">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1125">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1125">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1126">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1126">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1127">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1128">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1128">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1131">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1131">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1132">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1132">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1133">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1134">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1134">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1137">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1137">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1138">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1138">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1139">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1140">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1140">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1143">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1143">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1144">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1144">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1145">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1146">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1146">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1147">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1149">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1149">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1150">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1150">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1151">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1152">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1152">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1155">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1155">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1156">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1156">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1157">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1158">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1158">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1161">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1161">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1162">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1162">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1163">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1164">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1164">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1167">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1167">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1168">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1168">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1169">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1170">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1170">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1173">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1173">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1174">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1174">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1175">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1176">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1176">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1177">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1179">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1179">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1180">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1180">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1181">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1182">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1182">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1183">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1184">------------------ | | [Proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration) temas de *seguridad* | Azure Key Vault | | [Proveedor de configuración de aplicación de Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentación de Azure) | Configuración de aplicación de Azure | | [Proveedor de configuración de la línea de comandos](#command-line-configuration-provider) | Parámetros de la línea de comandos | | [Proveedor de configuración personalizada](#custom-configuration-provider) | Origen personalizado | | [Proveedor de configuración de variables de entorno](#environment-variables-configuration-provider) | Variables de entorno | | [Proveedor de configuración de archivo](#file-configuration-provider) | Archivos INI, JSON y XML | | [Proveedor de configuración de clave por archivo](#key-per-file-configuration-provider) | Archivos de directorio | | [Proveedor de configuración de memoria](#memory-configuration-provider) | Colecciones en memoria | | [Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) (temas de *seguridad*) | Archivo en el directorio del perfil de usuario |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="eaa1a-1185">Los orígenes de configuración se leen en el orden en que se especifican sus proveedores de configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="eaa1a-1186">En este tema, los proveedores de configuración se describen en orden alfabético y no en el orden en el que el código los organiza.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="eaa1a-1187">Ordene los proveedores de configuración en el código para cumplir con las prioridades relacionadas con los orígenes de configuración subyacentes que la aplicación necesita.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="eaa1a-1188">Esta es una secuencia típica de proveedores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="eaa1a-1189">Archivos (*appsettings.json*, *appsettings.{Environment}.json*, donde `{Environment}` es el entorno de hospedaje actual de la aplicación)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="eaa1a-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="eaa1a-1191">[Secretos de usuario (administrador de secretos)](xref:security/app-secrets) (solo para entornos de desarrollo)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="eaa1a-1192">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1192">Environment variables</span></span>
1. <span data-ttu-id="eaa1a-1193">Argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1193">Command-line arguments</span></span>

<span data-ttu-id="eaa1a-1194">Una práctica común es colocar el proveedor de configuración de línea de comandos al final en una serie de proveedores para permitir que los argumentos de la línea de comandos reemplacen la configuración establecida por el resto de los proveedores.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="eaa1a-1195">La secuencia de proveedores anterior se usa cuando se inicializa un nuevo generador de host con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa1a-1196">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="eaa1a-1197">Configurar el generador de host con UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="eaa1a-1198">Para configurar el generador de host, realice una llamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> en el generador de host con la configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="eaa1a-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="eaa1a-1200">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar los proveedores de configuración de la aplicación además de aquellos que `CreateDefaultBuilder` agrega automáticamente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="eaa1a-1201">Reemplazar la configuración anterior con argumentos de la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="eaa1a-1202">Para proporcionar configuración de aplicaciones que se pueda reemplazar por argumentos de la línea de comandos, llame a `AddCommandLine` en último lugar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="eaa1a-1203">Quitar proveedores agregados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="eaa1a-1204">Para quitar los proveedores agregados por `CreateDefaultBuilder`, llame primero a [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) en [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="eaa1a-1205">Usar la configuración durante el inicio de la aplicación</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="eaa1a-1206">La configuración proporcionada a la aplicación en `ConfigureAppConfiguration` está disponible durante el inicio de la aplicación, incluido `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eaa1a-1207">Para obtener más información, vea la sección [Acceso a la configuración durante el inicio](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="eaa1a-1208">Proveedor de configuración de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="eaa1a-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carga la configuración de pares clave-valor de argumento de la línea de comandos en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa1a-1210">Para activar la configuración de línea de comandos, se llama al método de extensión <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa1a-1211">`AddCommandLine` se llama automáticamente al llamar a `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="eaa1a-1212">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa1a-1213">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="eaa1a-1214">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="eaa1a-1215">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa1a-1216">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1216">Environment variables.</span></span>

<span data-ttu-id="eaa1a-1217">`CreateDefaultBuilder` agrega el proveedor de configuración de línea de comandos al final.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="eaa1a-1218">Los argumentos de la línea de comandos que se pasan en tiempo de ejecución invalidan la configuración establecida por los otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="eaa1a-1219">`CreateDefaultBuilder` actúa cuando se construye el host.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="eaa1a-1220">Por tanto, la configuración de línea de comandos activada por `CreateDefaultBuilder` puede afectar la manera en que se configura el host.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="eaa1a-1221">Para las aplicaciones basadas en plantillas de ASP.NET Core, `CreateDefaultBuilder` ya realiza una llamada a `AddCommandLine`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa1a-1222">Para agregar proveedores de configuración adicionales y mantener la capacidad de reemplazar la configuración de sus proveedores con argumentos de la línea de comandos, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddCommandLine` en último lugar.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="eaa1a-1223">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1223">**Example**</span></span>

<span data-ttu-id="eaa1a-1224">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="eaa1a-1225">Abra un símbolo del sistema en el directorio del proyecto.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="eaa1a-1226">Proporcione un argumento de la línea de comandos para el comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="eaa1a-1227">Una vez que se ejecuta la aplicación, abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="eaa1a-1228">Observe que la salida contiene el par clave-valor para el argumento de línea de comandos de configuración proporcionado a `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="eaa1a-1229">Argumentos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1229">Arguments</span></span>

<span data-ttu-id="eaa1a-1230">El valor debe seguir a un signo igual (`=`) o la clave debe tener un prefijo (`--` o `/`) cuando el valor siga a un espacio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="eaa1a-1231">El valor no es obligatorio si se usa un signo igual (por ejemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="eaa1a-1232">Prefijo de la clave</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1232">Key prefix</span></span>               | <span data-ttu-id="eaa1a-1233">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="eaa1a-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1235">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1235">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1236">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1236">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1237">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1238">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1238">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1241">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1241">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1242">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1242">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1243">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1244">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1244">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1247">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1247">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1248">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1248">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1249">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1250">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1250">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1253">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1253">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1254">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1254">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1255">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1256">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1256">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1259">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1259">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1260">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1260">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1261">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1262">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1262">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1265">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1265">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1266">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1266">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1267">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1268">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1268">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1271">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1271">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1272">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1272">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1273">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1274">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1274">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1277">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1277">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1278">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1278">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1279">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1280">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1280">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1283">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1283">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1284">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1284">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1285">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1286">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1286">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1289">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1289">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1290">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1290">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1291">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1292">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1292">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1295">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1295">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1296">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1296">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1297">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1298">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1298">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1301">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1301">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1302">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1302">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1303">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1304">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1304">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1307">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1307">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1308">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1308">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1309">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1310">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1310">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1313">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1313">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1314">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1314">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1315">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1316">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1316">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1319">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1319">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1320">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1320">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1321">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1322">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1322">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1325">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1325">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1326">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1326">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1327">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1328">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1328">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1331">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1331">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1332">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1332">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1333">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1334">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1334">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1337">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1337">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1338">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1338">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1339">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1340">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1340">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1343">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1343">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1344">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1344">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1345">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1346">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1346">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1349">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1349">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1350">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1350">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1351">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1352">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1352">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1355">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1355">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1356">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1356">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1357">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1358">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1358">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1361">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1361">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1362">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1362">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1363">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1364">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1364">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1367">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1367">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1368">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1368">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1369">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1370">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1370">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1373">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1373">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1374">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1374">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1375">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1376">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1376">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1379">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1379">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1380">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1380">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1381">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1382">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1382">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1385">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1385">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1386">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1386">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1387">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1388">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1388">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1391">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1391">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1392">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1392">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1393">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1394">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1394">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1397">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1397">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1398">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1398">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1399">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1400">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1400">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1403">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1403">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1404">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1404">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1405">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1406">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1406">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1409">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1409">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1410">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1410">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1411">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1412">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1412">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1415">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1415">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1416">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1416">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1417">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1418">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1418">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1421">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1421">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1422">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1422">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1423">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1424">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1424">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1427">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1427">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1428">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1428">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1429">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1430">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1430">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1433">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1433">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1434">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1434">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1435">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1436">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1436">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1439">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1439">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1440">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1440">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1441">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1442">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1442">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1444">--------------------------- | | Sin prefijo                | `CommandLineKey1=value1`                               |
| Dos guines (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Barra diagonal (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="eaa1a-1445">Dentro del mismo comando, no mezcle pares clave-valor de argumento de la línea de comandos que usan un signo igual con pares de clave-valor que usan un espacio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="eaa1a-1446">Comandos de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="eaa1a-1447">Asignaciones de modificador</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1447">Switch mappings</span></span>

<span data-ttu-id="eaa1a-1448">Las asignaciones de modificador admiten la lógica de sustitución de nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="eaa1a-1449">Al crear manualmente la configuración con <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, proporcione un diccionario de reemplazos de modificador al método <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="eaa1a-1450">Cuando se usa el diccionario de asignaciones de modificador, se comprueba en el diccionario si una clave coincide con la clave proporcionada por un argumento de línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="eaa1a-1451">Si la clave de la línea de comandos se encuentra en el diccionario, se devuelve el valor del diccionario (el reemplazo de la clave) para establecer el par clave-valor en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="eaa1a-1452">Se requiere una asignación de conmutador para cualquier clave de línea de comandos precedida por un solo guion (`-`).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="eaa1a-1453">Reglas de clave del diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="eaa1a-1454">Los modificadores deben empezar por un guion (`-`) o guion doble (`--`).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="eaa1a-1455">El diccionario de asignaciones de modificador no debe contener claves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="eaa1a-1456">Cree un diccionario de asignaciones de modificador.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="eaa1a-1457">En el ejemplo siguiente, se crean dos asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="eaa1a-1458">Al compilar el host, llame a `AddCommandLine` con el diccionario de asignaciones de modificador:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="eaa1a-1459">En el caso de las aplicaciones que usen las asignaciones de modificador, la llamada a `CreateDefaultBuilder` no tiene que pasar argumentos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="eaa1a-1460">En la llamada de `AddCommandLine` del método `CreateDefaultBuilder`, no se incluyen modificadores asignados y no hay forma de pasar el diccionario de asignación de modificador a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa1a-1461">La solución no es pasar los argumentos de `CreateDefaultBuilder`, sino que permitir que el método `AddCommandLine` del método `ConfigurationBuilder` procese tanto los argumentos como el diccionario de asignación de modificador.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="eaa1a-1462">Después de crear el diccionario de asignaciones de modificador, contiene los datos que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="eaa1a-1463">Key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1463">Key</span></span>       | <span data-ttu-id="eaa1a-1464">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1464">Value</span></span>             |
| ---
<span data-ttu-id="eaa1a-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1466">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1466">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1467">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1467">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1468">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1469">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1469">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1472">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1472">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1473">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1473">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1474">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1475">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1475">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1478">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1478">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1479">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1479">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1480">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1481">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1481">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1484">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1484">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1485">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1485">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1486">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1487">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1487">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1490">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1490">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1491">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1491">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1492">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1493">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1493">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1496">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1496">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1497">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1497">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1498">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1499">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1499">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1502">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1502">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1503">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1503">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1504">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1505">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1505">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1508">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1508">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1509">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1509">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1510">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1511">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1511">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="eaa1a-1514">Si las claves de asignación de conmutador se usan al iniciar la aplicación, la configuración recibe el valor de configuración en la clave que el diccionario suministra:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="eaa1a-1515">Una vez que se ejecuta el comando anterior, la configuración contiene los valores que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="eaa1a-1516">Key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1516">Key</span></span>               | <span data-ttu-id="eaa1a-1517">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1517">Value</span></span>    |
| ---
<span data-ttu-id="eaa1a-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1519">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1519">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1520">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1520">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1521">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1522">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1522">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1525">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1525">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1526">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1526">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1527">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1528">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1528">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1531">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1531">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1532">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1532">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1533">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1534">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1534">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1537">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1537">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1538">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1538">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1539">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1540">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1540">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1543">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1543">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1544">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1544">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1545">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1546">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1546">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1549">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1549">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1550">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1550">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1551">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1552">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1552">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1555">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1555">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1556">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1556">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1557">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1558">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1558">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1561">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1561">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1562">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1562">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1563">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1564">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1564">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="eaa1a-1567">Proveedor de configuración de variables de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="eaa1a-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carga la configuración de pares clave-valor de variables de entorno en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa1a-1569">Para activar la configuración de variables de entorno, llame al método de extensión <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="eaa1a-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permite establecer variables de entorno en Azure Portal que pueden invalidar la configuración de la aplicación mediante el proveedor de configuración de variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="eaa1a-1571">Para más información, consulte [Aplicaciones de Azure: Invalidación de la configuración de la aplicación mediante Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="eaa1a-1572">`AddEnvironmentVariables` se usa para cargar variables de entorno con el prefijo `ASPNETCORE_` para la [configuración de host](#host-versus-app-configuration) al inicializar un nuevo generador de host con el [host de web](xref:fundamentals/host/web-host) y al llamar a `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="eaa1a-1573">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa1a-1574">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="eaa1a-1575">Configuración de la aplicación desde variables de entorno sin prefijo mediante la llamada a `AddEnvironmentVariables` sin prefijo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="eaa1a-1576">Configuración opcional de los archivos *appsettings.json* y *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="eaa1a-1577">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa1a-1578">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1578">Command-line arguments.</span></span>

<span data-ttu-id="eaa1a-1579">El proveedor de configuración de variables de entorno se llama una vez establecida la configuración desde los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="eaa1a-1580">Llamar al proveedor en esta posición permite que la lectura de las variables de entorno en tiempo de ejecución invaliden la configuración establecida por los secretos de usuario y los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="eaa1a-1581">Para proporcionar la configuración de la aplicación desde variables de entorno adicionales, llame a los proveedores adicionales de la aplicación en `ConfigureAppConfiguration` y llame a `AddEnvironmentVariables` con el prefijo:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="eaa1a-1582">Llame a `AddEnvironmentVariables` en último lugar para permitir que las variables de entorno con el prefijo especificado invaliden los valores de otros proveedores.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="eaa1a-1583">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1583">**Example**</span></span>

<span data-ttu-id="eaa1a-1584">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="eaa1a-1585">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1585">Run the sample app.</span></span> <span data-ttu-id="eaa1a-1586">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="eaa1a-1587">Observe que el resultado contiene el par clave y valor para la variable de entorno `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="eaa1a-1588">El valor refleja el entorno en el que se ejecuta la aplicación, habitualmente `Development` cuando se ejecuta de manera local.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="eaa1a-1589">Para mantener un número adecuado de variables de entorno en la lista representada por la aplicación, la aplicación filtrará las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="eaa1a-1590">Vea el archivo *Pages/Index.cshtml.cs* de la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="eaa1a-1591">Para exponer todas las variables de entorno disponibles para la aplicación, cambie `FilteredConfiguration` en *Pages/Index.cshtml.cs* por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="eaa1a-1592">Prefijos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1592">Prefixes</span></span>

<span data-ttu-id="eaa1a-1593">Las variables de entorno que se cargan en la configuración de la aplicación se filtran cuando se proporciona un prefijo para el método `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="eaa1a-1594">Por ejemplo, para filtrar las variables de entorno en el prefijo `CUSTOM_`, suministre el prefijo para el proveedor de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="eaa1a-1595">El prefijo se quita cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="eaa1a-1596">Al crear el generador de host, las variables de entorno proporcionan la configuración del host.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="eaa1a-1597">Para obtener más información sobre el prefijo usado para estas variables de entorno, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa1a-1598">**Prefijos de cadena de conexión**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="eaa1a-1599">La API de configuración tiene reglas de procesamiento especial para cuatro variables de entorno de cadena de conexión relacionadas con la configuración de las cadenas de conexión de Azure para el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="eaa1a-1600">Las variables de entorno con los prefijos que se muestran en la tabla se cargan en la aplicación si no se proporciona ningún prefijo a `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="eaa1a-1601">Prefijo de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1601">Connection string prefix</span></span> | <span data-ttu-id="eaa1a-1602">Proveedor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1602">Provider</span></span> |
| ---
<span data-ttu-id="eaa1a-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1604">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1604">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1605">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1605">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1606">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1607">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1607">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1610">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1610">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1611">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1611">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1612">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1613">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1613">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1616">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1616">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1617">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1617">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1618">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1619">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1619">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1622">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1622">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1623">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1623">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1624">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1625">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1625">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1628">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1628">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1629">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1629">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1630">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1631">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1631">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1634">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1634">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1635">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1635">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1636">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1637">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1637">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1640">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1640">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1641">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1641">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1642">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1643">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1643">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1646">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1646">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1647">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1647">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1648">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1649">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1649">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1652">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1652">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1653">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1653">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1654">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1655">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1655">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1658">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1658">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1659">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1659">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1660">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1661">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1661">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1664">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1664">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1665">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1665">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1666">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1667">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1667">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1670">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1670">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1671">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1671">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1672">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1673">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1673">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1675">---- | | `CUSTOMCONNSTR_` | Proveedor personalizado | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="eaa1a-1676">Cuando una variable de entorno se detecta y carga en la configuración con cualquiera de los cuatro prefijos que se muestran en la tabla:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="eaa1a-1677">La clave de configuración se crea al quitar el prefijo de la variable de entorno y al agregar una sección de clave de configuración (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="eaa1a-1678">Se crea un nuevo par clave-valor de configuración que representa el proveedor de conexión de base de datos (excepto para `CUSTOMCONNSTR_`, que no tiene ningún proveedor indicado).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="eaa1a-1679">Clave de variable de entorno</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1679">Environment variable key</span></span> | <span data-ttu-id="eaa1a-1680">Clave de configuración convertida</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1680">Converted configuration key</span></span> | <span data-ttu-id="eaa1a-1681">Entrada de configuración del proveedor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="eaa1a-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1683">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1683">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1684">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1684">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1685">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1686">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1686">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1689">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1689">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1690">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1690">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1691">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1692">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1692">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1695">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1695">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1696">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1696">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1697">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1698">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1698">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1701">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1701">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1702">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1702">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1703">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1704">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1704">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1707">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1707">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1708">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1708">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1709">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1710">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1710">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1713">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1713">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1714">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1714">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1715">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1716">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1716">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1719">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1719">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1720">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1720">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1721">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1722">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1722">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1725">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1725">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1726">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1726">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1727">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1728">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1728">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1731">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1731">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1732">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1732">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1733">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1734">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1734">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1737">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1737">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1738">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1738">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1739">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1740">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1740">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1743">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1743">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1744">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1744">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1745">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1746">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1746">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1749">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1749">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1750">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1750">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1751">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1752">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1752">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1755">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1755">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1756">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1756">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1757">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1758">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1758">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1761">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1761">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1762">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1762">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1763">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1764">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1764">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1767">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1767">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1768">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1768">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1769">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1770">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1770">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1773">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1773">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1774">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1774">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1775">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1776">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1776">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1779">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1779">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1780">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1780">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1781">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1782">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1782">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1785">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1785">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1786">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1786">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1787">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1788">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1788">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1791">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1791">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1792">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1792">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1793">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1794">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1794">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1797">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1797">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1798">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1798">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1799">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1800">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1800">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1803">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1803">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1804">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1804">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1805">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1806">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1806">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1809">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1809">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1810">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1810">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1811">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1812">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1812">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1815">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1815">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1816">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1816">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1817">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1818">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1818">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1821">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1821">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1822">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1822">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1823">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1824">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1824">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1827">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1827">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1828">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1828">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1829">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1830">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1830">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1833">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1833">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1834">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1834">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1835">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1836">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1836">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1839">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1839">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1840">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1840">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1841">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1842">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1842">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1845">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1845">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1846">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1846">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1847">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1848">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1848">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1851">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1851">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1852">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1852">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1853">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1854">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1854">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1857">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1857">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1858">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1858">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1859">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1860">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1860">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1863">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1863">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1864">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1864">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1865">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1866">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1866">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1869">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1869">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1870">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1870">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1871">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1872">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1872">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1875">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1875">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1876">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1876">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1877">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1878">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1878">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1881">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1881">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1882">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1882">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1883">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1884">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1884">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1885">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1887">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1887">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1888">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1888">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1889">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1890">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1890">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1891">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1893">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1893">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1894">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1894">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1895">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1896">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1896">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1897">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1899">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1899">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1900">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1900">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1901">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1902">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1902">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1903">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1905">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1905">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1906">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1906">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1907">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1908">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1908">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1909">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1911">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1911">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1912">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1912">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1913">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1914">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1914">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1915">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1917">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1917">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1918">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1918">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1919">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1920">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1920">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1921">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1923">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1923">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1924">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1924">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1925">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1926">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1926">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1927">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1929">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1929">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1930">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1930">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1931">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1932">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1932">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1933">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1935">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1935">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1936">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1936">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1937">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1938">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1938">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1939">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1941">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1941">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1942">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1942">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1943">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1944">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1944">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1945">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1947">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1947">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1948">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1948">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1949">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1950">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1950">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1951">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1953">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1953">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1954">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1954">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1955">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1956">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1956">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1957">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1959">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1959">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1960">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1960">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1961">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1962">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1962">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1963">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1965">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1965">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1966">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1966">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1967">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1968">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1968">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1969">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1971">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1971">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1972">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1972">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1973">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1974">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1974">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1975">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1977">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1977">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1978">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1978">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1979">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1980">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1980">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1981">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1983">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1983">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1984">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1984">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1985">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1986">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1986">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1987">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1989">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1989">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1990">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1990">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1991">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1992">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1992">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1993">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-1995">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1995">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-1996">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1996">'Identity'</span></span>
- <span data-ttu-id="eaa1a-1997">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-1998">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1998">'Razor'</span></span>
- <span data-ttu-id="eaa1a-1999">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2001">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2001">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2002">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2002">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2003">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2004">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2004">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2005">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2007">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2007">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2008">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2008">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2009">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2010">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2010">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2011">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2013">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2013">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2014">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2014">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2015">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2016">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2016">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2017">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2019">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2019">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2020">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2020">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2021">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2022">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2022">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2023">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2025">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2025">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2026">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2026">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2027">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2028">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2028">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2029">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Entrada de configuración no creada.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="eaa1a-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="eaa1a-2032">Valor: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="eaa1a-2033">Valor: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Clave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="eaa1a-2034">Valor: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="eaa1a-2035">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2035">**Example**</span></span>

<span data-ttu-id="eaa1a-2036">Se crea una variable de entorno de una cadena de conexión personalizada en el servidor:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="eaa1a-2037">Nombre: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="eaa1a-2038">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="eaa1a-2039">Si `IConfiguration` se inserta y se asigna a un campo denominado `_config`, se lee el valor siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="eaa1a-2040">Proveedor de configuración de archivo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2040">File Configuration Provider</span></span>

<span data-ttu-id="eaa1a-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> es la clase base para cargar la configuración del sistema de archivos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="eaa1a-2042">Los proveedores de configuración siguientes están dedicados a determinados tipos de archivo:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="eaa1a-2043">Proveedor de configuración INI</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="eaa1a-2044">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="eaa1a-2045">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="eaa1a-2046">Proveedor de configuración de INI</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2046">INI Configuration Provider</span></span>

<span data-ttu-id="eaa1a-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carga la configuración desde pares clave-valor de archivo INI en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa1a-2048">Para activar la configuración de archivo INI, llame al método de extensión <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa1a-2049">Los dos puntos se pueden usar como un delimitador de sección en la configuración de archivo INI.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="eaa1a-2050">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa1a-2051">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa1a-2052">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="eaa1a-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="eaa1a-2054">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="eaa1a-2055">Un ejemplo genérico de un archivo de configuración INI:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2055">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="eaa1a-2056">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa1a-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2057">section0:key0</span></span>
* <span data-ttu-id="eaa1a-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2058">section0:key1</span></span>
* <span data-ttu-id="eaa1a-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2059">section1:subsection:key</span></span>
* <span data-ttu-id="eaa1a-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="eaa1a-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="eaa1a-2062">Proveedor de configuración JSON</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="eaa1a-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carga la configuración desde pares clave-valor de archivos JSON en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="eaa1a-2064">Para activar la configuración de archivo JSON, llame al método de extensión <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa1a-2065">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa1a-2066">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa1a-2067">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="eaa1a-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="eaa1a-2069">Se llama automáticamente a `AddJsonFile` dos veces cuando un nuevo generador de host se inicializa con `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="eaa1a-2070">Se llama al método para cargar la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="eaa1a-2071">*appsettings.json*: Este archivo se lee primero.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="eaa1a-2072">La versión del entorno del archivo puede invalidar los valores que proporciona el archivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="eaa1a-2073">*appsettings.{Environment}.json*: La versión del entorno del archivo se carga en función de [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="eaa1a-2074">Para obtener más información, vea la sección [Configuración predeterminada](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="eaa1a-2075">`CreateDefaultBuilder` también carga:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="eaa1a-2076">Variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2076">Environment variables.</span></span>
* <span data-ttu-id="eaa1a-2077">[Secretos de usuario (Administrador de secretos)](xref:security/app-secrets) en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="eaa1a-2078">Argumentos de la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2078">Command-line arguments.</span></span>

<span data-ttu-id="eaa1a-2079">El proveedor de configuración de JSON se establece en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="eaa1a-2080">Por tanto, los secretos de usuario, las variables de entorno y los argumentos de la línea de comandos invalidan la configuración establecida por los archivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="eaa1a-2081">Llame a `ConfigureAppConfiguration` al crear el host para especificar la configuración de la aplicación para archivos distintos de *appsettings.json* y *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="eaa1a-2082">**Ejemplo**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2082">**Example**</span></span>

<span data-ttu-id="eaa1a-2083">La aplicación de ejemplo aprovecha el método de conveniencia estático `CreateDefaultBuilder` para crear el host, que incluye una llamada a `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="eaa1a-2084">La primera llamada a `AddJsonFile` carga la configuración desde *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="eaa1a-2085">La segunda llamada a `AddJsonFile` carga la configuración desde *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="eaa1a-2086">Para *appsettings.Development.json* en la aplicación de ejemplo, se carga el siguiente archivo:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="eaa1a-2087">Ejecute la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2087">Run the sample app.</span></span> <span data-ttu-id="eaa1a-2088">Abra un explorador a la aplicación en `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="eaa1a-2089">La salida contiene pares clave-valor para la configuración basada en el entorno de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="eaa1a-2090">El nivel de registro de la clave `Logging:LogLevel:Default` es `Debug` al ejecutar la aplicación en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="eaa1a-2091">Vuelva a ejecutar la aplicación de ejemplo en el entorno de producción:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="eaa1a-2092">Abra el archivo *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="eaa1a-2093">En el perfil de `ConfigurationSample`, cambie el valor de la variable de entorno de `ASPNETCORE_ENVIRONMENT` por `Production`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="eaa1a-2094">Guarde el archivo y ejecute la aplicación con `dotnet run` en un shell de comandos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="eaa1a-2095">La configuración de *appsettings.Development.json* ya no invalida la configuración de *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="eaa1a-2096">El nivel de registro de la clave `Logging:LogLevel:Default` es `Warning`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="eaa1a-2097">Proveedor de configuración XML</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2097">XML Configuration Provider</span></span>

<span data-ttu-id="eaa1a-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carga la configuración desde pares clave-valor de archivo XML en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="eaa1a-2099">Para activar la configuración de archivo XML, llame al método de extensión <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa1a-2100">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa1a-2101">Si el archivo es opcional.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="eaa1a-2102">Si la configuración se recarga si el archivo cambia.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="eaa1a-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider> que se usa para acceder al archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="eaa1a-2104">El nodo raíz del archivo de configuración se omite cuando se crean los pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="eaa1a-2105">No especifique una definición de tipo de documento (DTD) ni el espacio de nombres en el archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="eaa1a-2106">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="eaa1a-2107">Los archivos de configuración XML pueden usar distintos nombres de elemento para las secciones repetidas:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="eaa1a-2108">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa1a-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2109">section0:key0</span></span>
* <span data-ttu-id="eaa1a-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2110">section0:key1</span></span>
* <span data-ttu-id="eaa1a-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2111">section1:key0</span></span>
* <span data-ttu-id="eaa1a-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2112">section1:key1</span></span>

<span data-ttu-id="eaa1a-2113">Los elementos de repetición que usan el mismo nombre de elemento funcionan si el atributo `name` se usa para distinguir los elementos:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="eaa1a-2114">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa1a-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="eaa1a-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="eaa1a-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="eaa1a-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2118">section:section1:key:key1</span></span>

<span data-ttu-id="eaa1a-2119">Los atributos se pueden usar para suministrar valores:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="eaa1a-2120">El archivo de configuración anterior carga las siguientes claves con `value`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="eaa1a-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2121">key:attribute</span></span>
* <span data-ttu-id="eaa1a-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="eaa1a-2123">Proveedor de configuración de clave por archivo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="eaa1a-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa los archivos de un directorio como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="eaa1a-2125">La clave es el nombre de archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2125">The key is the file name.</span></span> <span data-ttu-id="eaa1a-2126">El valor contiene el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2126">The value contains the file's contents.</span></span> <span data-ttu-id="eaa1a-2127">El proveedor de configuración de clave por archivo se usa en escenarios de hospedaje de Docker.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="eaa1a-2128">Para activar la configuración de clave por archivo, llame al método de extensión <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="eaa1a-2129">La ruta de acceso `directoryPath` a los archivos debe ser una ruta de acceso absoluta.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="eaa1a-2130">Las sobrecargas permiten especificar:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="eaa1a-2131">Un delegado `Action<KeyPerFileConfigurationSource>` que configura el origen.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="eaa1a-2132">Si el directorio es opcional y la ruta de acceso al directorio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="eaa1a-2133">El carácter de subrayado doble (`__`) se usa como delimitador de claves de configuración en los nombres de archivo.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="eaa1a-2134">Por ejemplo, el nombre de archivo `Logging__LogLevel__System` genera la clave de configuración `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="eaa1a-2135">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="eaa1a-2136">Proveedor de configuración de memoria</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="eaa1a-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa una colección en memoria como pares clave-valor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="eaa1a-2138">Para activar la configuración de colección en memoria, llame al método de extensión <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> en una instancia de <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="eaa1a-2139">El proveedor de configuración se puede inicializar con un `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="eaa1a-2140">Llame a `ConfigureAppConfiguration` cuando cree el host para especificar la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="eaa1a-2141">En el ejemplo siguiente, se crea un diccionario de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="eaa1a-2142">El diccionario se usa con una llamada a `AddInMemoryCollection` para proporcionar la configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="eaa1a-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2143">GetValue</span></span>

<span data-ttu-id="eaa1a-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrae un único valor de la configuración con una clave determinada y lo convierte al tipo especificado que no es de colección.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="eaa1a-2145">Una sobrecarga acepta un valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="eaa1a-2146">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2146">The following example:</span></span>

* <span data-ttu-id="eaa1a-2147">Se extrae el valor de cadena de la configuración con la clave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="eaa1a-2148">Si `NumberKey` no se encuentra en las claves de configuración, se usa el valor predeterminado de `99`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="eaa1a-2149">Se escribe el valor como `int`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="eaa1a-2150">Se almacena el valor en la propiedad `NumberConfig` para usarlo en la página.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="eaa1a-2151">GetSection, GetChildren y Exists</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="eaa1a-2152">Para los ejemplos siguientes, considere el siguiente archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="eaa1a-2153">Se encuentran cuatro claves en dos secciones, una de las cuales incluye un par de subsecciones:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="eaa1a-2154">Cuando el archivo se lee en la configuración, se crean las siguientes claves jerárquicas únicas para contener los valores de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="eaa1a-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2155">section0:key0</span></span>
* <span data-ttu-id="eaa1a-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2156">section0:key1</span></span>
* <span data-ttu-id="eaa1a-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2157">section1:key0</span></span>
* <span data-ttu-id="eaa1a-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2158">section1:key1</span></span>
* <span data-ttu-id="eaa1a-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="eaa1a-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="eaa1a-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="eaa1a-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="eaa1a-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2163">GetSection</span></span>

<span data-ttu-id="eaa1a-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrae una subsección de la configuración con la clave de subsección especificada.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="eaa1a-2165">Para devolver un <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contiene los pares clave-valor en `section1`, llame a `GetSection` y suministre el nombre de la sección:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="eaa1a-2166">`configSection` no tiene ningún valor, solo una clave y una ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="eaa1a-2167">De forma similar, para obtener los valores de las claves de `section2:subsection0`, llame a `GetSection` y suministre la ruta de acceso a la sección:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="eaa1a-2168">`GetSection` nunca devuelve `null`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="eaa1a-2169">Si no se encuentra una sección que coincida, se devuelve una `IConfigurationSection` vacía.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="eaa1a-2170">Cuando `GetSection` devuelve una sección coincidente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> no se rellena.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="eaa1a-2171">Se devuelven <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> y <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> si la sección existe.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="eaa1a-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2172">GetChildren</span></span>

<span data-ttu-id="eaa1a-2173">Una llamada a [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) en `section2` obtiene una `IEnumerable<IConfigurationSection>` que incluye:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="eaa1a-2174">Existe</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2174">Exists</span></span>

<span data-ttu-id="eaa1a-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar si existe una sección de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="eaa1a-2176">Dados los datos de ejemplo, `sectionExists` es `false` porque no hay una sección `section2:subsection2` en los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="eaa1a-2177">Enlazar a un gráfico de objetos</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2177">Bind to an object graph</span></span>

<span data-ttu-id="eaa1a-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> es capaz de enlazar todo un gráfico de objetos POCO.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="eaa1a-2179">Al igual que ocurre con el enlace de un objeto simple, solo se enlazan las propiedades públicas de lectura o escritura.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="eaa1a-2180">El ejemplo contiene un modelo `TvShow` cuyo gráfico de objetos incluye las clases `Metadata` y `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2181">La aplicación de ejemplo tiene un archivo *tvshow.xml* que contiene los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="eaa1a-2182">Configuración está enlazada a todo el gráfico de objetos `TvShow`con el método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="eaa1a-2183">La instancia enlazada se asigna a una propiedad para la representación:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="eaa1a-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) enlaza y devuelve el tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="eaa1a-2185">`Get<T>` es más conveniente que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="eaa1a-2186">En el código siguiente se muestra cómo usar `Get<T>` con el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="eaa1a-2187">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2187">Bind an array to a class</span></span>

<span data-ttu-id="eaa1a-2188">*La aplicación de ejemplo muestra los conceptos que se explican en esta sección.*</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="eaa1a-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> admite enlazar matrices a objetos con los índices de matriz en las claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="eaa1a-2190">Cualquier formato de matriz que expone un segmento de clave numérica (`:0:`, `:1:`, &hellip; `:{n}:`) es capaz de enlazar una matriz a una matriz de clase POCO.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="eaa1a-2191">El enlace se proporciona por convención.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2191">Binding is provided by convention.</span></span> <span data-ttu-id="eaa1a-2192">Los proveedores de configuración personalizados no son necesarios para implementar el enlace de matriz.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="eaa1a-2193">**Procesamiento de matriz en memoria**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2193">**In-memory array processing**</span></span>

<span data-ttu-id="eaa1a-2194">Considere los valores y las claves de configuración que se muestran en esta tabla.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="eaa1a-2195">Key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2195">Key</span></span>             | <span data-ttu-id="eaa1a-2196">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2196">Value</span></span>  |
| :---
<span data-ttu-id="eaa1a-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2198">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2198">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2199">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2199">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2200">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2201">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2201">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2204">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2204">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2205">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2205">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2206">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2207">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2207">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2210">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2210">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2211">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2211">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2212">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2213">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2213">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2216">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2216">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2217">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2217">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2218">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2219">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2219">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="eaa1a-2222">Estas claves y valores se cargan en la aplicación de ejemplo a través del proveedor de configuración de memoria:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="eaa1a-2223">La matriz omite un valor de índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="eaa1a-2224">El enlazador de configuración no es capaz de enlazar los valores NULL ni de crear entradas NULL en los objetos enlazados, lo que queda claro cuando se muestra el resultado de enlazar esta matriz a un objeto.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="eaa1a-2225">En la aplicación de ejemplo, hay disponible una clase POCO para almacenar los datos de configuración enlazados:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2226">Los datos de configuración están enlazados al objeto:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="eaa1a-2227">También se puede usar la sintaxis [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*), lo que genera un código más compacto:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="eaa1a-2228">El objeto enlazado, una instancia de `ArrayExample`, recibe los datos de la matriz desde la configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="eaa1a-2229">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="eaa1a-2230">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="eaa1a-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2232">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2232">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2233">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2233">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2234">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2235">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2235">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2238">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2238">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2239">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2239">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2240">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2241">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2241">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2244">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2244">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2245">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2245">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2246">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2247">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2247">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2250">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2250">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2251">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2251">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2252">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2253">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2253">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2256">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2256">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2257">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2257">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2258">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2259">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2259">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2262">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2262">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2263">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2263">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2264">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2265">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2265">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2268">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2268">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2269">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2269">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2270">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2271">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2271">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2274">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2274">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2275">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2275">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2276">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2277">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2277">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2280">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2280">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2281">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2281">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2282">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2283">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2283">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2286">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2286">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2287">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2287">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2288">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2289">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2289">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2292">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2292">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2293">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2293">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2294">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2295">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2295">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2298">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2298">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2299">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2299">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2300">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2301">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2301">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2304">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2304">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2305">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2305">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2306">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2307">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2307">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2310">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2310">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2311">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2311">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2312">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2313">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2313">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2316">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2316">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2317">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2317">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2318">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2319">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2319">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2322">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2322">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2323">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2323">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2324">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2325">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2325">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2328">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2328">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2329">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2329">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2330">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2331">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2331">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2334">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2334">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2335">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2335">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2336">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2337">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2337">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2340">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2340">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2341">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2341">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2342">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2343">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2343">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2346">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2347">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2349">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2352">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2353">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2355">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2358">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2359">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2361">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="eaa1a-2364">El índice &num;3 en el objeto enlazado contiene los datos de configuración para la clave de configuración `array:4` y su valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="eaa1a-2365">Cuando se enlazan datos de configuración que contienen una matriz, los índices de la matriz en las claves de configuración solo se usan para iterar los datos de configuración al crear el objeto.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="eaa1a-2366">No se puede conservar un valor NULL en los datos de configuración y no se crea una entrada con valores NULL en un objeto enlazado cuando una matriz en las claves de configuración omite uno o más índices.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="eaa1a-2367">El elemento de configuración omitido para el índice &num;3 se puede proporcionar antes del enlace a la instancia `ArrayExample` por cualquier proveedor de configuración que genera el par clave-valor correcto en la configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="eaa1a-2368">Si el ejemplo incluía un proveedor de configuración JSON adicional con el par clave-valor omitido, `ArrayExample.Entries` coincide con la matriz de configuración completa:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="eaa1a-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="eaa1a-2370">En `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="eaa1a-2371">El par clave-valor que se muestra en la tabla se carga en la configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="eaa1a-2372">Key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2372">Key</span></span>             | <span data-ttu-id="eaa1a-2373">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2373">Value</span></span>  |
| :---
<span data-ttu-id="eaa1a-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2375">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2375">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2376">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2376">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2377">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2378">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2378">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2379">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2381">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2381">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2382">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2382">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2383">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2384">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2384">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2387">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2387">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2388">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2388">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2389">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2390">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2390">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2393">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2393">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2394">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2394">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2395">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2396">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2396">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="eaa1a-2399">Si la instancia de clase `ArrayExample` se enlaza después de que el proveedor de configuración JSON incluye la entrada para el índice &num;3, la matriz `ArrayExample.Entries` incluye el valor.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="eaa1a-2400">Índice de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="eaa1a-2401">Valor de `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="eaa1a-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2403">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2403">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2404">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2404">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2405">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2406">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2406">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2409">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2409">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2410">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2410">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2411">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2412">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2412">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2415">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2415">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2416">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2416">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2417">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2418">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2418">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2421">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2421">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2422">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2422">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2423">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2424">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2424">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2427">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2427">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2428">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2428">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2429">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2430">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2430">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2433">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2433">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2434">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2434">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2435">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2436">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2436">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2439">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2439">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2440">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2440">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2441">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2442">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2442">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2445">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2445">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2446">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2446">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2447">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2448">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2448">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2451">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2451">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2452">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2452">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2453">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2454">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2454">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2457">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2457">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2458">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2458">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2459">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2460">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2460">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2463">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2463">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2464">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2464">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2465">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2466">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2466">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2469">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2469">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2470">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2470">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2471">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2472">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2472">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2475">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2475">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2476">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2476">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2477">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2478">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2478">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2481">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2481">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2482">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2482">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2483">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2484">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2484">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2487">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2487">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2488">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2488">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2489">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2490">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2490">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2493">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2493">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2494">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2494">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2495">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2496">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2496">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2499">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2499">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2500">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2500">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2501">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2502">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2502">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2505">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2505">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2506">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2506">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2507">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2508">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2508">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2511">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2511">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2512">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2512">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2513">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2514">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2514">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2517">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2517">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2518">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2518">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2519">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2520">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2520">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2523">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2523">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2524">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2524">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2525">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2526">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2526">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2529">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2529">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2530">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2530">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2531">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2532">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2532">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="eaa1a-2535">**Procesamiento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2535">**JSON array processing**</span></span>

<span data-ttu-id="eaa1a-2536">Si un archivo JSON contiene una matriz, se crean claves de configuración para los elementos de la matriz con un índice de sección basado en cero.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="eaa1a-2537">En el siguiente archivo de configuración, `subsection` es una matriz:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="eaa1a-2538">El proveedor de configuración JSON lee los datos de configuración en los siguientes pares clave-valor:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="eaa1a-2539">Key</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2539">Key</span></span>                     | <span data-ttu-id="eaa1a-2540">Valor</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2540">Value</span></span>  |
| ---
<span data-ttu-id="eaa1a-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2542">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2542">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2543">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2543">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2544">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2545">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2545">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2548">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2548">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2549">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2549">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2550">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2551">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2551">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2554">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2554">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2555">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2555">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2556">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2557">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2557">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2560">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2560">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2561">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2561">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2562">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2563">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2563">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2566">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2566">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2567">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2567">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2568">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2569">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2569">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2572">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2572">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2573">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2573">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2574">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2575">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2575">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2578">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2578">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2579">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2579">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2580">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2581">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2581">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2584">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2584">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2585">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2585">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2586">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2587">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2587">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2590">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2590">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2591">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2591">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2592">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2593">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2593">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="eaa1a-2596">En la aplicación de ejemplo, la clase POCO siguiente está disponible para enlazar los pares clave-valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2597">Después del enlace, `JsonArrayExample.Key` contiene el valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="eaa1a-2598">Los valores de la subsección se almacenan en la propiedad de la matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="eaa1a-2599">Índice de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="eaa1a-2600">Valor de `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="eaa1a-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2602">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2602">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2603">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2603">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2604">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2605">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2605">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2608">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2608">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2609">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2609">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2610">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2611">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2611">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2614">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2614">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2615">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2615">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2616">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2617">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2617">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2620">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2620">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2621">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2621">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2622">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2623">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2623">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2626">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2626">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2627">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2627">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2628">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2629">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2629">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2632">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2632">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2633">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2633">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2634">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2635">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2635">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2638">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2638">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2639">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2639">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2640">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2641">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2641">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2644">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2644">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2645">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2645">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2646">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2647">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2647">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2650">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2650">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2651">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2651">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2652">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2653">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2653">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2656">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2656">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2657">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2657">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2658">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2659">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2659">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2662">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2662">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2663">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2663">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2664">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2665">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2665">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2668">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2668">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2669">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2669">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2670">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2671">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2671">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2674">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2674">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2675">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2675">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2676">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2677">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2677">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2678">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2680">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2680">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2681">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2681">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2682">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2683">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2683">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2686">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2686">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2687">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2687">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2688">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2689">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2689">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2692">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2692">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2693">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2693">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2694">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2695">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2695">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2698">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2698">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2699">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2699">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2700">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2701">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2701">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2704">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2704">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2705">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2705">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2706">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2707">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2707">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2710">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2710">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2711">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2711">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2712">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2713">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2713">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2716">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2716">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2717">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2717">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2718">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2719">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2719">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2722">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2722">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2723">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2723">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2724">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2725">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2725">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2728">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2728">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2729">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2729">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2730">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2731">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2731">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2734">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2734">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2735">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2735">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2736">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2737">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2737">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2740">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2740">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2741">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2741">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2742">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2743">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2743">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2746">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2746">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2747">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2747">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2748">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2749">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2749">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2752">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2752">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2753">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2753">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2754">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2755">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2755">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2758">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2758">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2759">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2759">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2760">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2761">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2761">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="eaa1a-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="eaa1a-2764">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2764">'Blazor'</span></span>
- <span data-ttu-id="eaa1a-2765">"Identity"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2765">'Identity'</span></span>
- <span data-ttu-id="eaa1a-2766">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="eaa1a-2767">"Razor"</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2767">'Razor'</span></span>
- <span data-ttu-id="eaa1a-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="eaa1a-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="eaa1a-2770">Proveedores de configuración personalizada</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2770">Custom configuration provider</span></span>

<span data-ttu-id="eaa1a-2771">La aplicación de ejemplo muestra cómo crear un proveedor de configuración básica que lee los pares clave-valor de configuración desde una base de datos mediante [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="eaa1a-2772">El proveedor tiene las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="eaa1a-2773">La base de datos en memoria de EF se usa para fines de demostración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="eaa1a-2774">Para usar una base de datos que requiere una cadena de conexión, implemente un `ConfigurationBuilder` secundario para suministrar la cadena de conexión desde otro proveedor de configuración.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="eaa1a-2775">El proveedor lee una tabla de base de datos en la configuración en el inicio.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="eaa1a-2776">El proveedor no consulta la base de datos por clave.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="eaa1a-2777">La función de recarga en cambio no se implementa, por lo que actualizar la base de datos después del inicio de la aplicación no afecta a la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="eaa1a-2778">Defina una entidad `EFConfigurationValue` para almacenar los valores de configuración en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="eaa1a-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2780">Agregue un `EFConfigurationContext` para almacenar y tener acceso a los valores configurados.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="eaa1a-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2782">Cree una clase que implemente <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="eaa1a-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2784">Cree el proveedor de configuración personalizado heredando de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="eaa1a-2785">El proveedor de configuración inicializa la base de datos cuando está vacía.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="eaa1a-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2787">Un método de extensión `AddEFConfiguration` permite agregar el origen de configuración a un `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="eaa1a-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="eaa1a-2789">En el código siguiente se muestra cómo puede usar el `EFConfigurationProvider` personalizado en *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="eaa1a-2790">Acceso a la configuración durante el inicio</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2790">Access configuration during startup</span></span>

<span data-ttu-id="eaa1a-2791">Inserte `IConfiguration` en el constructor `Startup` para acceder a los valores de configuración en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eaa1a-2792">Para acceder a la configuración en `Startup.Configure`, inserte `IConfiguration` directamente en el método o use la instancia desde el constructor:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="eaa1a-2793">Para ver un ejemplo de cómo acceder a la configuración mediante métodos de conveniencia de inicio, consulte [Inicio de la aplicación: Métodos de conveniencia](xref:fundamentals/startup#convenience-methods)</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="eaa1a-2794">Acceso a la configuración en una página de Razor Pages o en una vista de MVC</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="eaa1a-2795">Para acceder a los valores de configuración en una página de Razor Pages o una vista de MVC, agregue una [directiva using](xref:mvc/views/razor#using) ([Referencia de C#: directiva using](/dotnet/csharp/language-reference/keywords/using-directive)) para el [espacio de nombres Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) e inyecte <xref:Microsoft.Extensions.Configuration.IConfiguration> en la página o en la vista.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="eaa1a-2796">En una página de Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2796">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="eaa1a-2797">En una vista de MVC:</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2797">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="eaa1a-2798">Agregar configuración a partir de un ensamblado externo</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="eaa1a-2799">Una implementación de <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo fuera de la clase `Startup` de esta.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="eaa1a-2800">Para obtener más información, vea <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eaa1a-2801">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="eaa1a-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
