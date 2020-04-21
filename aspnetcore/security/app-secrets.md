---
title: Almacenamiento seguro de secretos de aplicaciones en desarrollo en ASP.NET Core
author: rick-anderson
description: Obtén información sobre cómo almacenar y recuperar información confidencial como secretos de aplicación durante el desarrollo de una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661798"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="17899-103">Almacenamiento seguro de secretos de aplicaciones en desarrollo en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17899-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17899-104">Por [Rick Anderson,](https://twitter.com/RickAndMSFT) [Kirk Larkin,](https://twitter.com/serpent5) [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="17899-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="17899-105">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17899-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="17899-106">En este documento se explican las técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="17899-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="17899-107">Nunca almacene contraseñas u otros datos confidenciales en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="17899-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="17899-108">Los secretos de producción no deben usarse para el desarrollo o la prueba.</span><span class="sxs-lookup"><span data-stu-id="17899-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="17899-109">Los secretos no deben implementarse con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17899-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="17899-110">En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el proveedor de configuración de [Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="17899-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="17899-111">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="17899-111">Environment variables</span></span>

<span data-ttu-id="17899-112">Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en el código o en archivos de configuración local.</span><span class="sxs-lookup"><span data-stu-id="17899-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="17899-113">Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="17899-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="17899-114">Considere una aplicación web ASP.NET Core en la que está habilitada la seguridad de cuentas de **usuario individuales.**</span><span class="sxs-lookup"><span data-stu-id="17899-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="17899-115">Se incluye una cadena de conexión de base de datos `DefaultConnection`predeterminada en el archivo *appsettings.json* del proyecto con la clave .</span><span class="sxs-lookup"><span data-stu-id="17899-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="17899-116">La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña.</span><span class="sxs-lookup"><span data-stu-id="17899-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="17899-117">Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="17899-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="17899-118">La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.</span><span class="sxs-lookup"><span data-stu-id="17899-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="17899-119">Las variables de entorno generalmente se almacenan en texto sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="17899-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="17899-120">Si la máquina o el proceso se ven comprometidos, las partes que no son de confianza pueden acceder a las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="17899-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="17899-121">Es posible que se requieran medidas adicionales para evitar la divulgación de secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="17899-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="17899-122">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="17899-122">Secret Manager</span></span>

<span data-ttu-id="17899-123">La herramienta Secret Manager almacena datos confidenciales durante el desarrollo de un proyecto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17899-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="17899-124">En este contexto, un fragmento de datos confidenciales es un secreto de aplicación.</span><span class="sxs-lookup"><span data-stu-id="17899-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="17899-125">Los secretos de aplicación se almacenan en una ubicación independiente del árbol del proyecto.</span><span class="sxs-lookup"><span data-stu-id="17899-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="17899-126">Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="17899-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="17899-127">Los secretos de la aplicación no se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="17899-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="17899-128">La herramienta Secret Manager no cifra los secretos almacenados y no debe tratarse como un almacén de confianza.</span><span class="sxs-lookup"><span data-stu-id="17899-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="17899-129">Es sólo para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="17899-129">It's for development purposes only.</span></span> <span data-ttu-id="17899-130">Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio de perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="17899-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="17899-131">Cómo funciona la herramienta Secret Manager</span><span class="sxs-lookup"><span data-stu-id="17899-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="17899-132">La herramienta Secret Manager abstrae los detalles de implementación, como dónde y cómo se almacenan los valores.</span><span class="sxs-lookup"><span data-stu-id="17899-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="17899-133">Puede utilizar la herramienta sin conocer estos detalles de implementación.</span><span class="sxs-lookup"><span data-stu-id="17899-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="17899-134">Los valores se almacenan en un archivo de configuración JSON en una carpeta de perfil de usuario protegida por el sistema en el equipo local:</span><span class="sxs-lookup"><span data-stu-id="17899-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="17899-135">Windows</span><span class="sxs-lookup"><span data-stu-id="17899-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="17899-136">Ruta del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="17899-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="17899-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="17899-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="17899-138">Ruta del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="17899-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="17899-139">En las rutas de `<user_secrets_id>` acceso `UserSecretsId` de archivo anteriores, sustitúyala por el valor especificado en el archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="17899-140">No escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="17899-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="17899-141">Estos detalles de implementación pueden cambiar.</span><span class="sxs-lookup"><span data-stu-id="17899-141">These implementation details may change.</span></span> <span data-ttu-id="17899-142">Por ejemplo, los valores secretos no se cifran, pero podrían estar en el futuro.</span><span class="sxs-lookup"><span data-stu-id="17899-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="17899-143">Habilitar el almacenamiento secreto</span><span class="sxs-lookup"><span data-stu-id="17899-143">Enable secret storage</span></span>

<span data-ttu-id="17899-144">La herramienta Secret Manager funciona en los valores de configuración específicos del proyecto almacenados en su perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="17899-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="17899-145">La herramienta Secret `init` Manager incluye un comando en .NET Core SDK 3.0.100 o posterior.</span><span class="sxs-lookup"><span data-stu-id="17899-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="17899-146">Para utilizar secretos de usuario, ejecute el siguiente comando en el directorio del proyecto:</span><span class="sxs-lookup"><span data-stu-id="17899-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="17899-147">El comando anterior agrega `UserSecretsId` un `PropertyGroup` elemento dentro de un archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="17899-148">De forma predeterminada, `UserSecretsId` el texto interno de es un GUID.</span><span class="sxs-lookup"><span data-stu-id="17899-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="17899-149">El texto interno es arbitrario, pero es único para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="17899-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="17899-150">En Visual Studio, haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione **Administrar secretos** de usuario en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="17899-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="17899-151">Este gesto `UserSecretsId` agrega un elemento, rellenado con un GUID, al archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="17899-152">Establecer un secreto</span><span class="sxs-lookup"><span data-stu-id="17899-152">Set a secret</span></span>

<span data-ttu-id="17899-153">Defina un secreto de aplicación que consta de una clave y su valor.</span><span class="sxs-lookup"><span data-stu-id="17899-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="17899-154">El secreto está asociado con `UserSecretsId` el valor del proyecto.</span><span class="sxs-lookup"><span data-stu-id="17899-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="17899-155">Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="17899-156">En el ejemplo anterior, los `Movies` dos puntos denota que es un literal de objeto con una `ServiceApiKey` propiedad.</span><span class="sxs-lookup"><span data-stu-id="17899-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="17899-157">La herramienta Secret Manager también se puede utilizar desde otros directorios.</span><span class="sxs-lookup"><span data-stu-id="17899-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="17899-158">Utilice `--project` la opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="17899-159">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="17899-160">Aplanamiento de estructura JSON en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17899-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="17899-161">El gesto **Administrar secretos** de usuario de Visual Studio abre un archivo *secrets.json* en el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="17899-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="17899-162">Reemplace el contenido de *secrets.json* por los pares clave-valor que se van a almacenar.</span><span class="sxs-lookup"><span data-stu-id="17899-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="17899-163">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="17899-164">La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` o `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="17899-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="17899-165">Por ejemplo, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` ejecución contrae el literal del objeto.</span><span class="sxs-lookup"><span data-stu-id="17899-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="17899-166">El archivo modificado es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="17899-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="17899-167">Establecer varios secretos</span><span class="sxs-lookup"><span data-stu-id="17899-167">Set multiple secrets</span></span>

<span data-ttu-id="17899-168">Un lote de secretos se puede `set` establecer canalizando JSON al comando.</span><span class="sxs-lookup"><span data-stu-id="17899-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="17899-169">En el ejemplo siguiente, el contenido del archivo *input.json* se canaliza al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="17899-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="17899-170">Windows</span><span class="sxs-lookup"><span data-stu-id="17899-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="17899-171">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17899-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="17899-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="17899-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="17899-173">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17899-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="17899-174">Acceder a un secreto</span><span class="sxs-lookup"><span data-stu-id="17899-174">Access a secret</span></span>

<span data-ttu-id="17899-175">La API de [configuración de ASP.NET Core](xref:fundamentals/configuration/index) proporciona acceso a secretos de Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="17899-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="17899-176">En ASP.NET Core 2.0 o posterior, el origen de configuración de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama para inicializar una nueva instancia del host con valores predeterminados preconfigurados.</span><span class="sxs-lookup"><span data-stu-id="17899-176">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="17899-177">`CreateDefaultBuilder`llamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> cuando <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>el es :</span><span class="sxs-lookup"><span data-stu-id="17899-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="17899-178">Cuando `CreateDefaultBuilder` no se llama, agregue explícitamente el <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> origen `Startup` de configuración de secretos de usuario llamando al constructor.</span><span class="sxs-lookup"><span data-stu-id="17899-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="17899-179">Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="17899-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="17899-180">Los secretos de usuario `Configuration` se pueden recuperar a través de la API:</span><span class="sxs-lookup"><span data-stu-id="17899-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="17899-181">Mapa de secretos a un POCO</span><span class="sxs-lookup"><span data-stu-id="17899-181">Map secrets to a POCO</span></span>

<span data-ttu-id="17899-182">Asignar un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="17899-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-183">Para asignar los secretos anteriores `Configuration` a un POCO, utilice la característica de enlace de gráfico de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API.</span><span class="sxs-lookup"><span data-stu-id="17899-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="17899-184">El código siguiente se `MovieSettings` enlaza a un `ServiceApiKey` POCO personalizado y tiene acceso al valor de propiedad:</span><span class="sxs-lookup"><span data-stu-id="17899-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="17899-185">Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan `MovieSettings`a las propiedades respectivas en:</span><span class="sxs-lookup"><span data-stu-id="17899-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="17899-186">Reemplazo de cuerdas con secretos</span><span class="sxs-lookup"><span data-stu-id="17899-186">String replacement with secrets</span></span>

<span data-ttu-id="17899-187">Almacenar contraseñas en texto sin formato es inseguro.</span><span class="sxs-lookup"><span data-stu-id="17899-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="17899-188">Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:</span><span class="sxs-lookup"><span data-stu-id="17899-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="17899-189">Un enfoque más seguro es almacenar la contraseña como un secreto.</span><span class="sxs-lookup"><span data-stu-id="17899-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="17899-190">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="17899-191">Quite `Password` el par clave-valor de la cadena de conexión en *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="17899-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="17899-192">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-192">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="17899-193">El valor del secreto se <xref:System.Data.SqlClient.SqlConnectionStringBuilder> puede establecer <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> en la propiedad de un objeto para completar la cadena de conexión:</span><span class="sxs-lookup"><span data-stu-id="17899-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="17899-194">Lista de los secretos</span><span class="sxs-lookup"><span data-stu-id="17899-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-195">Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="17899-196">Se mostrará lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="17899-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="17899-197">En el ejemplo anterior, dos puntos de los nombres de clave denota la jerarquía de objetos dentro de *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="17899-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="17899-198">Eliminar un solo secreto</span><span class="sxs-lookup"><span data-stu-id="17899-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-199">Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="17899-200">El archivo *secrets.json* de la aplicación se modificó para `MoviesConnectionString` quitar el par clave-valor asociado a la clave:</span><span class="sxs-lookup"><span data-stu-id="17899-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="17899-201">`dotnet user-secrets list`muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="17899-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="17899-202">Eliminar todos los secretos</span><span class="sxs-lookup"><span data-stu-id="17899-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-203">Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="17899-204">Todos los secretos de usuario de la aplicación se han eliminado del archivo *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="17899-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="17899-205">En `dotnet user-secrets list` ejecución se muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="17899-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="17899-206">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17899-206">Additional resources</span></span>

* <span data-ttu-id="17899-207">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener información sobre el acceso a Secret Manager desde IIS.</span><span class="sxs-lookup"><span data-stu-id="17899-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17899-208">Por [Rick Anderson,](https://twitter.com/RickAndMSFT) [Daniel Roth](https://github.com/danroth27)y Scott [Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="17899-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="17899-209">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17899-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="17899-210">En este documento se explican las técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="17899-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="17899-211">Nunca almacene contraseñas u otros datos confidenciales en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="17899-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="17899-212">Los secretos de producción no deben usarse para el desarrollo o la prueba.</span><span class="sxs-lookup"><span data-stu-id="17899-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="17899-213">Los secretos no deben implementarse con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17899-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="17899-214">En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el proveedor de configuración de [Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="17899-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="17899-215">Variables de entorno</span><span class="sxs-lookup"><span data-stu-id="17899-215">Environment variables</span></span>

<span data-ttu-id="17899-216">Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en el código o en archivos de configuración local.</span><span class="sxs-lookup"><span data-stu-id="17899-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="17899-217">Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="17899-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="17899-218">Considere una aplicación web ASP.NET Core en la que está habilitada la seguridad de cuentas de **usuario individuales.**</span><span class="sxs-lookup"><span data-stu-id="17899-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="17899-219">Se incluye una cadena de conexión de base de datos `DefaultConnection`predeterminada en el archivo *appsettings.json* del proyecto con la clave .</span><span class="sxs-lookup"><span data-stu-id="17899-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="17899-220">La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña.</span><span class="sxs-lookup"><span data-stu-id="17899-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="17899-221">Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="17899-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="17899-222">La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.</span><span class="sxs-lookup"><span data-stu-id="17899-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="17899-223">Las variables de entorno generalmente se almacenan en texto sin cifrar.</span><span class="sxs-lookup"><span data-stu-id="17899-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="17899-224">Si la máquina o el proceso se ven comprometidos, las partes que no son de confianza pueden acceder a las variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="17899-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="17899-225">Es posible que se requieran medidas adicionales para evitar la divulgación de secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="17899-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="17899-226">Administrador de secretos</span><span class="sxs-lookup"><span data-stu-id="17899-226">Secret Manager</span></span>

<span data-ttu-id="17899-227">La herramienta Secret Manager almacena datos confidenciales durante el desarrollo de un proyecto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17899-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="17899-228">En este contexto, un fragmento de datos confidenciales es un secreto de aplicación.</span><span class="sxs-lookup"><span data-stu-id="17899-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="17899-229">Los secretos de aplicación se almacenan en una ubicación independiente del árbol del proyecto.</span><span class="sxs-lookup"><span data-stu-id="17899-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="17899-230">Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos.</span><span class="sxs-lookup"><span data-stu-id="17899-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="17899-231">Los secretos de la aplicación no se protegen en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="17899-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="17899-232">La herramienta Secret Manager no cifra los secretos almacenados y no debe tratarse como un almacén de confianza.</span><span class="sxs-lookup"><span data-stu-id="17899-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="17899-233">Es sólo para fines de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="17899-233">It's for development purposes only.</span></span> <span data-ttu-id="17899-234">Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio de perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="17899-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="17899-235">Cómo funciona la herramienta Secret Manager</span><span class="sxs-lookup"><span data-stu-id="17899-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="17899-236">La herramienta Secret Manager abstrae los detalles de implementación, como dónde y cómo se almacenan los valores.</span><span class="sxs-lookup"><span data-stu-id="17899-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="17899-237">Puede utilizar la herramienta sin conocer estos detalles de implementación.</span><span class="sxs-lookup"><span data-stu-id="17899-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="17899-238">Los valores se almacenan en un archivo de configuración JSON en una carpeta de perfil de usuario protegida por el sistema en el equipo local:</span><span class="sxs-lookup"><span data-stu-id="17899-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="17899-239">Windows</span><span class="sxs-lookup"><span data-stu-id="17899-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="17899-240">Ruta del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="17899-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="17899-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="17899-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="17899-242">Ruta del sistema de archivos:</span><span class="sxs-lookup"><span data-stu-id="17899-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="17899-243">En las rutas de `<user_secrets_id>` acceso `UserSecretsId` de archivo anteriores, sustitúyala por el valor especificado en el archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="17899-244">No escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="17899-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="17899-245">Estos detalles de implementación pueden cambiar.</span><span class="sxs-lookup"><span data-stu-id="17899-245">These implementation details may change.</span></span> <span data-ttu-id="17899-246">Por ejemplo, los valores secretos no se cifran, pero podrían estar en el futuro.</span><span class="sxs-lookup"><span data-stu-id="17899-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="17899-247">Habilitar el almacenamiento secreto</span><span class="sxs-lookup"><span data-stu-id="17899-247">Enable secret storage</span></span>

<span data-ttu-id="17899-248">La herramienta Secret Manager funciona en los valores de configuración específicos del proyecto almacenados en su perfil de usuario.</span><span class="sxs-lookup"><span data-stu-id="17899-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="17899-249">Para usar secretos `UserSecretsId` de usuario, defina un elemento dentro de un `PropertyGroup` archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="17899-250">El texto `UserSecretsId` interno de es arbitrario, pero es único para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="17899-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="17899-251">Los desarrolladores suelen `UserSecretsId`generar un GUID para el archivo .</span><span class="sxs-lookup"><span data-stu-id="17899-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="17899-252">En Visual Studio, haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione **Administrar secretos** de usuario en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="17899-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="17899-253">Este gesto `UserSecretsId` agrega un elemento, rellenado con un GUID, al archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="17899-254">Establecer un secreto</span><span class="sxs-lookup"><span data-stu-id="17899-254">Set a secret</span></span>

<span data-ttu-id="17899-255">Defina un secreto de aplicación que consta de una clave y su valor.</span><span class="sxs-lookup"><span data-stu-id="17899-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="17899-256">El secreto está asociado con `UserSecretsId` el valor del proyecto.</span><span class="sxs-lookup"><span data-stu-id="17899-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="17899-257">Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="17899-258">En el ejemplo anterior, los `Movies` dos puntos denota que es un literal de objeto con una `ServiceApiKey` propiedad.</span><span class="sxs-lookup"><span data-stu-id="17899-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="17899-259">La herramienta Secret Manager también se puede utilizar desde otros directorios.</span><span class="sxs-lookup"><span data-stu-id="17899-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="17899-260">Utilice `--project` la opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="17899-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="17899-261">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="17899-262">Aplanamiento de estructura JSON en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17899-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="17899-263">El gesto **Administrar secretos** de usuario de Visual Studio abre un archivo *secrets.json* en el editor de texto.</span><span class="sxs-lookup"><span data-stu-id="17899-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="17899-264">Reemplace el contenido de *secrets.json* por los pares clave-valor que se van a almacenar.</span><span class="sxs-lookup"><span data-stu-id="17899-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="17899-265">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="17899-266">La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` o `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="17899-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="17899-267">Por ejemplo, `dotnet user-secrets remove "Movies:ConnectionString"` la `Movies` ejecución contrae el literal del objeto.</span><span class="sxs-lookup"><span data-stu-id="17899-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="17899-268">El archivo modificado es similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="17899-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="17899-269">Establecer varios secretos</span><span class="sxs-lookup"><span data-stu-id="17899-269">Set multiple secrets</span></span>

<span data-ttu-id="17899-270">Un lote de secretos se puede `set` establecer canalizando JSON al comando.</span><span class="sxs-lookup"><span data-stu-id="17899-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="17899-271">En el ejemplo siguiente, el contenido del archivo *input.json* se canaliza al `set` comando.</span><span class="sxs-lookup"><span data-stu-id="17899-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="17899-272">Windows</span><span class="sxs-lookup"><span data-stu-id="17899-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="17899-273">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17899-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="17899-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="17899-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="17899-275">Abra un shell de comandos y ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="17899-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="17899-276">Acceder a un secreto</span><span class="sxs-lookup"><span data-stu-id="17899-276">Access a secret</span></span>

<span data-ttu-id="17899-277">La API de [configuración de ASP.NET Core](xref:fundamentals/configuration/index) proporciona acceso a secretos de Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="17899-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="17899-278">Si el proyecto tiene como destino .NET Framework, instale el paquete NuGet [Microsoft.Extensions.Configuration.UserSecrets.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)</span><span class="sxs-lookup"><span data-stu-id="17899-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>


<span data-ttu-id="17899-279">En ASP.NET Core 2.0 o posterior, el origen de configuración de <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama para inicializar una nueva instancia del host con valores predeterminados preconfigurados.</span><span class="sxs-lookup"><span data-stu-id="17899-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="17899-280">`CreateDefaultBuilder`llamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> cuando <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>el es :</span><span class="sxs-lookup"><span data-stu-id="17899-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


<span data-ttu-id="17899-281">Cuando `CreateDefaultBuilder` no se llama, agregue explícitamente el <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> origen `Startup` de configuración de secretos de usuario llamando al constructor.</span><span class="sxs-lookup"><span data-stu-id="17899-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="17899-282">Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="17899-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="17899-283">Los secretos de usuario `Configuration` se pueden recuperar a través de la API:</span><span class="sxs-lookup"><span data-stu-id="17899-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="17899-284">Mapa de secretos a un POCO</span><span class="sxs-lookup"><span data-stu-id="17899-284">Map secrets to a POCO</span></span>

<span data-ttu-id="17899-285">Asignar un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="17899-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-286">Para asignar los secretos anteriores `Configuration` a un POCO, utilice la característica de enlace de gráfico de [objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API.</span><span class="sxs-lookup"><span data-stu-id="17899-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="17899-287">El código siguiente se `MovieSettings` enlaza a un `ServiceApiKey` POCO personalizado y tiene acceso al valor de propiedad:</span><span class="sxs-lookup"><span data-stu-id="17899-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="17899-288">Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan `MovieSettings`a las propiedades respectivas en:</span><span class="sxs-lookup"><span data-stu-id="17899-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="17899-289">Reemplazo de cuerdas con secretos</span><span class="sxs-lookup"><span data-stu-id="17899-289">String replacement with secrets</span></span>

<span data-ttu-id="17899-290">Almacenar contraseñas en texto sin formato es inseguro.</span><span class="sxs-lookup"><span data-stu-id="17899-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="17899-291">Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:</span><span class="sxs-lookup"><span data-stu-id="17899-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="17899-292">Un enfoque más seguro es almacenar la contraseña como un secreto.</span><span class="sxs-lookup"><span data-stu-id="17899-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="17899-293">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="17899-294">Quite `Password` el par clave-valor de la cadena de conexión en *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="17899-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="17899-295">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="17899-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="17899-296">El valor del secreto se <xref:System.Data.SqlClient.SqlConnectionStringBuilder> puede establecer <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> en la propiedad de un objeto para completar la cadena de conexión:</span><span class="sxs-lookup"><span data-stu-id="17899-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="17899-297">Lista de los secretos</span><span class="sxs-lookup"><span data-stu-id="17899-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-298">Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="17899-299">Se mostrará lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="17899-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="17899-300">En el ejemplo anterior, dos puntos de los nombres de clave denota la jerarquía de objetos dentro de *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="17899-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="17899-301">Eliminar un solo secreto</span><span class="sxs-lookup"><span data-stu-id="17899-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-302">Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="17899-303">El archivo *secrets.json* de la aplicación se modificó para `MoviesConnectionString` quitar el par clave-valor asociado a la clave:</span><span class="sxs-lookup"><span data-stu-id="17899-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="17899-304">En `dotnet user-secrets list` ejecución se muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="17899-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="17899-305">Eliminar todos los secretos</span><span class="sxs-lookup"><span data-stu-id="17899-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="17899-306">Ejecute el siguiente comando desde el directorio en el que existe el archivo *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="17899-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="17899-307">Todos los secretos de usuario de la aplicación se han eliminado del archivo *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="17899-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="17899-308">En `dotnet user-secrets list` ejecución se muestra el siguiente mensaje:</span><span class="sxs-lookup"><span data-stu-id="17899-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="17899-309">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17899-309">Additional resources</span></span>

* <span data-ttu-id="17899-310">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener información sobre el acceso a Secret Manager desde IIS.</span><span class="sxs-lookup"><span data-stu-id="17899-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end