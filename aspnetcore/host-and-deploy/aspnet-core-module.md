---
title: Módulo ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo configurar el módulo de ASP.NET Core para hospedar aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c9cc340c9a3d6ff1b3783686bd779dfe04b14275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775276"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="145c6-103">Módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="145c6-103">ASP.NET Core Module</span></span>

<span data-ttu-id="145c6-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) y [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="145c6-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="145c6-105">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:</span><span class="sxs-lookup"><span data-stu-id="145c6-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="145c6-106">Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="145c6-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="145c6-107">Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="145c6-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="145c6-108">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="145c6-108">Supported Windows versions:</span></span>

* <span data-ttu-id="145c6-109">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="145c6-109">Windows 7 or later</span></span>
* <span data-ttu-id="145c6-110">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="145c6-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="145c6-111">En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="145c6-112">Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="145c6-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="145c6-113">El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="145c6-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="145c6-114">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="145c6-115">Modelo de hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="145c6-115">In-process hosting model</span></span>

<span data-ttu-id="145c6-116">Las aplicaciones ASP.NET Core usan de forma predeterminada el modelo de hospedaje dentro de proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="145c6-117">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="145c6-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="145c6-118">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="145c6-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="145c6-119">En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="145c6-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="145c6-120">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="145c6-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="145c6-121">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="145c6-122">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="145c6-123">El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="145c6-124">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="145c6-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="145c6-125">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-125">Use one app pool per app.</span></span>

* <span data-ttu-id="145c6-126">Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="145c6-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="145c6-127">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="145c6-128">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="145c6-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="145c6-129">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="145c6-129">Client disconnects are detected.</span></span> <span data-ttu-id="145c6-130">El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="145c6-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="145c6-131">En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="145c6-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="145c6-132">Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="145c6-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="145c6-133">Llame al método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="145c6-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="145c6-134">Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="145c6-135">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="145c6-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="145c6-136">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="145c6-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="145c6-137">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="145c6-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="145c6-138">No se admiten [implementaciones de paquetes web (archivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages).</span><span class="sxs-lookup"><span data-stu-id="145c6-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="145c6-139">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="145c6-139">Out-of-process hosting model</span></span>

<span data-ttu-id="145c6-140">Para configurar una aplicación para un hospedaje fuera de proceso, establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` en el archivo del proyecto ( *.csproj*):</span><span class="sxs-lookup"><span data-stu-id="145c6-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="145c6-141">El hospedaje en proceso se establece con `InProcess`, que es el valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="145c6-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="145c6-142">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="145c6-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="145c6-143">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="145c6-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="145c6-144">Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="145c6-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="145c6-145">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="145c6-146">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="145c6-147">Cambios del modelo de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-147">Hosting model changes</span></span>

<span data-ttu-id="145c6-148">Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="145c6-149">En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual.</span><span class="sxs-lookup"><span data-stu-id="145c6-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="145c6-150">La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="145c6-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="145c6-151">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="145c6-151">Process name</span></span>

<span data-ttu-id="145c6-152">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="145c6-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="145c6-153">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="145c6-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="145c6-154">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="145c6-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="145c6-155">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="145c6-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="145c6-156">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="145c6-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="145c6-157">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="145c6-158">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="145c6-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="145c6-159">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="145c6-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="145c6-160">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="145c6-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="145c6-161">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="145c6-161">Configuration with web.config</span></span>

<span data-ttu-id="145c6-162">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="145c6-163">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="145c6-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="145c6-164">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="145c6-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="145c6-165">La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).</span><span class="sxs-lookup"><span data-stu-id="145c6-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="145c6-166">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="145c6-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="145c6-167">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="145c6-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="145c6-168">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="145c6-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="145c6-169">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="145c6-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="145c6-170">Atributo</span><span class="sxs-lookup"><span data-stu-id="145c6-170">Attribute</span></span> | <span data-ttu-id="145c6-171">Descripción</span><span class="sxs-lookup"><span data-stu-id="145c6-171">Description</span></span> | <span data-ttu-id="145c6-172">Default</span><span class="sxs-lookup"><span data-stu-id="145c6-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="145c6-173">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-173">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-174">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="145c6-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="145c6-175">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-176">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="145c6-177">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-178">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="145c6-179">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="145c6-180">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-180">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-181">Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="145c6-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="145c6-182">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-183">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="145c6-184">&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="145c6-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="145c6-185">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="145c6-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="145c6-186">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="145c6-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="145c6-187">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="145c6-187">Default: `1`</span></span><br><span data-ttu-id="145c6-188">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="145c6-188">Min: `1`</span></span><br><span data-ttu-id="145c6-189">Máximo: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="145c6-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="145c6-190">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="145c6-190">Required string attribute.</span></span></p><p><span data-ttu-id="145c6-191">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="145c6-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="145c6-192">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="145c6-192">Relative paths are supported.</span></span> <span data-ttu-id="145c6-193">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="145c6-194">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-195">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="145c6-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="145c6-196">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="145c6-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="145c6-197">No admitido con el hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="145c6-198">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="145c6-198">Default: `10`</span></span><br><span data-ttu-id="145c6-199">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-199">Min: `0`</span></span><br><span data-ttu-id="145c6-200">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="145c6-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="145c6-201">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="145c6-202">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="145c6-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="145c6-203">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="145c6-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="145c6-204">No se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="145c6-205">En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="145c6-206">Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59.</span><span class="sxs-lookup"><span data-stu-id="145c6-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="145c6-207">El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</span><span class="sxs-lookup"><span data-stu-id="145c6-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="145c6-208">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-208">Default: `00:02:00`</span></span><br><span data-ttu-id="145c6-209">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-209">Min: `00:00:00`</span></span><br><span data-ttu-id="145c6-210">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="145c6-211">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-212">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="145c6-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="145c6-213">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="145c6-213">Default: `10`</span></span><br><span data-ttu-id="145c6-214">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-214">Min: `0`</span></span><br><span data-ttu-id="145c6-215">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="145c6-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="145c6-216">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-217">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="145c6-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="145c6-218">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="145c6-219">El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="145c6-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="145c6-220">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="145c6-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="145c6-221">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="145c6-221">Default: `120`</span></span><br><span data-ttu-id="145c6-222">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-222">Min: `0`</span></span><br><span data-ttu-id="145c6-223">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="145c6-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="145c6-224">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-225">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="145c6-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="145c6-226">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-226">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-227">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="145c6-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="145c6-228">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="145c6-229">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="145c6-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="145c6-230">Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="145c6-231">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="145c6-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="145c6-232">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="145c6-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="145c6-233">Establecimiento de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="145c6-233">Set environment variables</span></span>

<span data-ttu-id="145c6-234">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="145c6-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="145c6-235">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="145c6-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="145c6-236">Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="145c6-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="145c6-237">El ejemplo siguiente establece dos variables de entorno en *web.config*. `ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación en `Development`.</span><span class="sxs-lookup"><span data-stu-id="145c6-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="145c6-238">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="145c6-239">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="145c6-240">Una alternativa a establecer directamente el entorno en *web.config* consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="145c6-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="145c6-241">Este método establece el entorno en *web.config* cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="145c6-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="145c6-242">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="145c6-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="145c6-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="145c6-243">app_offline.htm</span></span>

<span data-ttu-id="145c6-244">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="145c6-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="145c6-245">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="145c6-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="145c6-246">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="145c6-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="145c6-247">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="145c6-248">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="145c6-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="145c6-249">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="145c6-250">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="145c6-250">Start-up error page</span></span>

<span data-ttu-id="145c6-251">Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="145c6-252">Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.</span><span class="sxs-lookup"><span data-stu-id="145c6-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="145c6-253">Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.</span><span class="sxs-lookup"><span data-stu-id="145c6-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="145c6-254">En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="145c6-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="145c6-255">Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="145c6-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="145c6-256">Para obtener más información sobre cómo configurar los mensajes de error personalizados, consulte [Errores HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="145c6-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="145c6-257">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="145c6-257">Log creation and redirection</span></span>

<span data-ttu-id="145c6-258">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="145c6-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="145c6-259">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="145c6-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="145c6-260">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="145c6-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="145c6-261">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="145c6-262">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="145c6-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="145c6-263">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="145c6-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="145c6-264">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="145c6-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="145c6-265">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="145c6-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="145c6-266">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="145c6-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="145c6-267">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="145c6-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="145c6-268">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="145c6-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="145c6-269">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="145c6-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="145c6-270">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="145c6-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="145c6-271">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="145c6-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="145c6-272">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="145c6-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="145c6-273">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="145c6-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="145c6-274">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="145c6-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="145c6-275">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="145c6-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="145c6-276">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="145c6-277">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="145c6-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="145c6-278">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="145c6-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="145c6-279">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="145c6-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="145c6-280">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` de *web.config*. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="145c6-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="145c6-281">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso (*logs* en el ejemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="145c6-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="145c6-282">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="145c6-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="145c6-283">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="145c6-284">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="145c6-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="145c6-285">ERROR</span><span class="sxs-lookup"><span data-stu-id="145c6-285">ERROR</span></span>
* <span data-ttu-id="145c6-286">WARNING</span><span class="sxs-lookup"><span data-stu-id="145c6-286">WARNING</span></span>
* <span data-ttu-id="145c6-287">INFO</span><span class="sxs-lookup"><span data-stu-id="145c6-287">INFO</span></span>
* <span data-ttu-id="145c6-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="145c6-288">TRACE</span></span>

<span data-ttu-id="145c6-289">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="145c6-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="145c6-290">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="145c6-290">CONSOLE</span></span>
* <span data-ttu-id="145c6-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="145c6-291">EVENTLOG</span></span>
* <span data-ttu-id="145c6-292">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="145c6-292">FILE</span></span>

<span data-ttu-id="145c6-293">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="145c6-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="145c6-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="145c6-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="145c6-295">(El valor predeterminado es *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="145c6-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="145c6-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="145c6-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="145c6-297">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="145c6-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="145c6-298">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="145c6-298">The size of the log isn't limited.</span></span> <span data-ttu-id="145c6-299">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="145c6-300">Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="145c6-301">Modificación del tamaño de la pila</span><span class="sxs-lookup"><span data-stu-id="145c6-301">Modify the stack size</span></span>

<span data-ttu-id="145c6-302">*Solo se aplica cuando se usa el modelo de hospedaje dentro de proceso.*</span><span class="sxs-lookup"><span data-stu-id="145c6-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="145c6-303">Configure el tamaño de la pila administrada mediante el valor `stackSize` en bytes en *web.config*. El tamaño predeterminado es `1048576` bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="145c6-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="145c6-304">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="145c6-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="145c6-305">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="145c6-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="145c6-306">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="145c6-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="145c6-307">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="145c6-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="145c6-308">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="145c6-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="145c6-309">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="145c6-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="145c6-310">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="145c6-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="145c6-311">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="145c6-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="145c6-312">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="145c6-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="145c6-313">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="145c6-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="145c6-314">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="145c6-315">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="145c6-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="145c6-316">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="145c6-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="145c6-317">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="145c6-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="145c6-318">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="145c6-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="145c6-319">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="145c6-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="145c6-320">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="145c6-321">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="145c6-321">Run the installer.</span></span>
1. <span data-ttu-id="145c6-322">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="145c6-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="145c6-323">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="145c6-324">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="145c6-325">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="145c6-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="145c6-326">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="145c6-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="145c6-327">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="145c6-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="145c6-328">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="145c6-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="145c6-329">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="145c6-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="145c6-330">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="145c6-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="145c6-331">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="145c6-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="145c6-332">Module</span><span class="sxs-lookup"><span data-stu-id="145c6-332">Module</span></span>

<span data-ttu-id="145c6-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="145c6-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="145c6-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="145c6-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="145c6-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="145c6-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="145c6-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="145c6-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="145c6-343">Schema</span><span class="sxs-lookup"><span data-stu-id="145c6-343">Schema</span></span>

<span data-ttu-id="145c6-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="145c6-344">**IIS**</span></span>

* <span data-ttu-id="145c6-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="145c6-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="145c6-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="145c6-347">**IIS Express**</span></span>

* <span data-ttu-id="145c6-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="145c6-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="145c6-350">Configuración</span><span class="sxs-lookup"><span data-stu-id="145c6-350">Configuration</span></span>

<span data-ttu-id="145c6-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="145c6-351">**IIS**</span></span>

* <span data-ttu-id="145c6-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="145c6-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="145c6-353">**IIS Express**</span></span>

* <span data-ttu-id="145c6-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="145c6-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="145c6-356">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="145c6-357">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para:</span><span class="sxs-lookup"><span data-stu-id="145c6-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="145c6-358">Hospedar una aplicación ASP.NET Core dentro del proceso de trabajo de IIS (`w3wp.exe`), lo que se denomina [modelo de hospedaje en proceso](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="145c6-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="145c6-359">Reenviar solicitudes web a una aplicación ASP.NET Core de back-end que ejecuta el [servidor de Kestrel](xref:fundamentals/servers/kestrel), lo que se denomina [modelo de hospedaje fuera de proceso](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="145c6-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="145c6-360">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="145c6-360">Supported Windows versions:</span></span>

* <span data-ttu-id="145c6-361">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="145c6-361">Windows 7 or later</span></span>
* <span data-ttu-id="145c6-362">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="145c6-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="145c6-363">En el caso del hospedaje en proceso, el módulo usa el servidor HTTP de IIS (`IISHttpServer`), una implementación de servidor en proceso de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="145c6-364">Cuando se hospeda fuera de proceso, el módulo solo funciona con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="145c6-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="145c6-365">El módulo no funciona con [HTTP. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="145c6-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="145c6-366">Modelos de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="145c6-367">Modelo de hospedaje en proceso</span><span class="sxs-lookup"><span data-stu-id="145c6-367">In-process hosting model</span></span>

<span data-ttu-id="145c6-368">Para configurar una aplicación para el hospedaje en proceso, agregue la propiedad `<AspNetCoreHostingModel>` al archivo de proyecto de la aplicación con un valor de `InProcess` (el hospedaje fuera de proceso se establece con `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="145c6-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="145c6-369">No se admite el modelo de hospedaje en proceso para aplicaciones de ASP.NET Core que tienen como destino .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="145c6-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="145c6-370">El valor de `<AspNetCoreHostingModel>` no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="145c6-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="145c6-371">Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="145c6-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="145c6-372">Al hospedar en proceso, se aplican las siguientes características:</span><span class="sxs-lookup"><span data-stu-id="145c6-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="145c6-373">En lugar del servidor HTTP de IIS (`IISHttpServer`) se usa el servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="145c6-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="145c6-374">En el mismo proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="145c6-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="145c6-375">Registrar el `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="145c6-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="145c6-376">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="145c6-377">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="145c6-378">El [atributo requestTimeout](#attributes-of-the-aspnetcore-element) no se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="145c6-379">No se admite el uso compartido de un grupo de aplicaciones entre aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="145c6-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="145c6-380">Se usa un grupo de aplicaciones por aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-380">Use one app pool per app.</span></span>

* <span data-ttu-id="145c6-381">Cuando se usa [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) o se coloca manualmente un [archivo app_offline.htm en la implementación](xref:host-and-deploy/iis/index#locked-deployment-files), puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="145c6-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="145c6-382">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="145c6-383">La arquitectura (valor de bits) de la aplicación y el runtime instalado (x64 o x86) deben coincidir con la arquitectura del grupo de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="145c6-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="145c6-384">Se detectan las desconexiones del cliente.</span><span class="sxs-lookup"><span data-stu-id="145c6-384">Client disconnects are detected.</span></span> <span data-ttu-id="145c6-385">El token de cancelación [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) se cancela cuando el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="145c6-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="145c6-386">En ASP.NET Core 2.2.1 o versiones anteriores, <xref:System.IO.Directory.GetCurrentDirectory*> devuelve el directorio de trabajo del proceso iniciado por IIS en lugar del de la aplicación (por ejemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="145c6-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="145c6-387">Para conocer el código de ejemplo que establece el directorio actual de la aplicación, consulte la información sobre la [clase CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="145c6-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="145c6-388">Llame al método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="145c6-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="145c6-389">Las llamadas subsiguientes a <xref:System.IO.Directory.GetCurrentDirectory*> proporcionan el directorio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="145c6-390">Cuando se hospeda en el proceso, no se llama a <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> de forma interna para inicializar un usuario.</span><span class="sxs-lookup"><span data-stu-id="145c6-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="145c6-391">Por tanto, se usa una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> para transformar las notificaciones después de que cada autenticación no se active de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="145c6-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="145c6-392">Al transformar notificaciones con una implementación de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, llame a <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para agregar servicios de autenticación:</span><span class="sxs-lookup"><span data-stu-id="145c6-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="145c6-393">Modelo de hospedaje fuera de proceso</span><span class="sxs-lookup"><span data-stu-id="145c6-393">Out-of-process hosting model</span></span>

<span data-ttu-id="145c6-394">Para configurar una aplicación para el hospedaje fuera de proceso, use uno de los métodos siguientes en el archivo de proyecto:</span><span class="sxs-lookup"><span data-stu-id="145c6-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="145c6-395">No especifique la propiedad `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="145c6-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="145c6-396">Si la propiedad `<AspNetCoreHostingModel>` no está presente en el archivo, el valor predeterminado es `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="145c6-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="145c6-397">Establezca el valor de la propiedad `<AspNetCoreHostingModel>` en `OutOfProcess` (el hospedaje en proceso se establece con `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="145c6-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="145c6-398">El valor no distingue mayúsculas de minúsculas, por lo que `inprocess` y `outofprocess` son valores válidos.</span><span class="sxs-lookup"><span data-stu-id="145c6-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="145c6-399">En lugar del servidor [Kestrel](xref:fundamentals/servers/kestrel) se usa el servidor HTTP de IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="145c6-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="145c6-400">Fuera del proceso, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) llama a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="145c6-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="145c6-401">Configurar el puerto y la ruta de acceso base donde debe escuchar el servidor al ejecutarse detrás del módulo de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="145c6-402">Configurar el host para capturar errores de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="145c6-403">Cambios del modelo de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-403">Hosting model changes</span></span>

<span data-ttu-id="145c6-404">Si se modifica el valor `hostingModel` en el archivo *web.config* (se explica en la sección [Configuración con web.config](#configuration-with-webconfig)), el módulo recicla el proceso de trabajo de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="145c6-405">En IIS Express, el módulo no recicla el proceso de trabajo, sino que desencadena un cierre estable del proceso de IIS Express actual.</span><span class="sxs-lookup"><span data-stu-id="145c6-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="145c6-406">La siguiente solicitud a la aplicación genera un nuevo proceso de IIS Express.</span><span class="sxs-lookup"><span data-stu-id="145c6-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="145c6-407">Nombre del proceso</span><span class="sxs-lookup"><span data-stu-id="145c6-407">Process name</span></span>

<span data-ttu-id="145c6-408">`Process.GetCurrentProcess().ProcessName` informa a `w3wp`/`iisexpress` (en proceso) o `dotnet` (fuera de proceso).</span><span class="sxs-lookup"><span data-stu-id="145c6-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="145c6-409">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="145c6-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="145c6-410">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="145c6-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="145c6-411">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="145c6-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="145c6-412">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="145c6-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="145c6-413">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="145c6-414">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="145c6-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="145c6-415">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="145c6-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="145c6-416">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="145c6-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="145c6-417">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="145c6-417">Configuration with web.config</span></span>

<span data-ttu-id="145c6-418">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="145c6-419">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="145c6-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="145c6-420">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="145c6-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="145c6-421">La propiedad <xref:System.Configuration.SectionInformation.InheritInChildApplications*> está establecida en `false` para indicar que las aplicaciones que residen en un subdirectorio de la aplicación no heredan la configuración especificada en el elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location).</span><span class="sxs-lookup"><span data-stu-id="145c6-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="145c6-422">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="145c6-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="145c6-423">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="145c6-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="145c6-424">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="145c6-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="145c6-425">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="145c6-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="145c6-426">Atributo</span><span class="sxs-lookup"><span data-stu-id="145c6-426">Attribute</span></span> | <span data-ttu-id="145c6-427">Descripción</span><span class="sxs-lookup"><span data-stu-id="145c6-427">Description</span></span> | <span data-ttu-id="145c6-428">Default</span><span class="sxs-lookup"><span data-stu-id="145c6-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="145c6-429">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-429">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-430">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="145c6-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="145c6-431">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-432">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="145c6-433">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-434">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="145c6-435">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="145c6-436">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-436">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-437">Especifica el modelo de hospedaje como en proceso (`InProcess`/`inprocess`) o fuera de proceso (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="145c6-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="145c6-438">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-439">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="145c6-440">&dagger;En el hospedaje en proceso, el valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="145c6-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="145c6-441">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="145c6-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="145c6-442">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="145c6-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="145c6-443">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="145c6-443">Default: `1`</span></span><br><span data-ttu-id="145c6-444">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="145c6-444">Min: `1`</span></span><br><span data-ttu-id="145c6-445">Máximo: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="145c6-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="145c6-446">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="145c6-446">Required string attribute.</span></span></p><p><span data-ttu-id="145c6-447">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="145c6-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="145c6-448">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="145c6-448">Relative paths are supported.</span></span> <span data-ttu-id="145c6-449">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="145c6-450">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-451">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="145c6-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="145c6-452">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="145c6-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="145c6-453">No admitido con el hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="145c6-454">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="145c6-454">Default: `10`</span></span><br><span data-ttu-id="145c6-455">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-455">Min: `0`</span></span><br><span data-ttu-id="145c6-456">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="145c6-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="145c6-457">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="145c6-458">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="145c6-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="145c6-459">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="145c6-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="145c6-460">No se aplica al hospedaje en proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="145c6-461">En el hospedaje en proceso, el módulo espera a que la aplicación procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="145c6-462">Los valores válidos para los segmentos de minutos y segundos de la cadena se encuentran en el rango 0-59.</span><span class="sxs-lookup"><span data-stu-id="145c6-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="145c6-463">El uso de **60** en el valor de minutos o segundos da como resultado el error *500: Error interno del servidor*.</span><span class="sxs-lookup"><span data-stu-id="145c6-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="145c6-464">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-464">Default: `00:02:00`</span></span><br><span data-ttu-id="145c6-465">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-465">Min: `00:00:00`</span></span><br><span data-ttu-id="145c6-466">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="145c6-467">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-468">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="145c6-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="145c6-469">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="145c6-469">Default: `10`</span></span><br><span data-ttu-id="145c6-470">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-470">Min: `0`</span></span><br><span data-ttu-id="145c6-471">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="145c6-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="145c6-472">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-473">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="145c6-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="145c6-474">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="145c6-475">El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="145c6-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="145c6-476">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="145c6-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="145c6-477">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="145c6-477">Default: `120`</span></span><br><span data-ttu-id="145c6-478">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-478">Min: `0`</span></span><br><span data-ttu-id="145c6-479">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="145c6-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="145c6-480">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-481">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="145c6-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="145c6-482">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-482">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-483">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="145c6-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="145c6-484">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="145c6-485">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="145c6-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="145c6-486">Al crearse el archivo de registro, el módulo crea las carpetas que se proporcionan en la ruta de acceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="145c6-487">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="145c6-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="145c6-488">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="145c6-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="145c6-489">Configuración de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="145c6-489">Setting environment variables</span></span>

<span data-ttu-id="145c6-490">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="145c6-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="145c6-491">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="145c6-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="145c6-492">Las variables de entorno establecidas en esta sección tienen prioridad sobre las variables del entorno del sistema.</span><span class="sxs-lookup"><span data-stu-id="145c6-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="145c6-493">En el ejemplo siguiente se establecen dos variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="145c6-493">The following example sets two environment variables.</span></span> <span data-ttu-id="145c6-494">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="145c6-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="145c6-495">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="145c6-496">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="145c6-497">Una alternativa a establecer directamente el entorno en *web.config* consiste en incluir la propiedad `<EnvironmentName>` en el [perfil de publicación (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) o el archivo de proyecto.</span><span class="sxs-lookup"><span data-stu-id="145c6-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="145c6-498">Este método establece el entorno en *web.config* cuando se publica el proyecto:</span><span class="sxs-lookup"><span data-stu-id="145c6-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="145c6-499">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="145c6-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="145c6-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="145c6-500">app_offline.htm</span></span>

<span data-ttu-id="145c6-501">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="145c6-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="145c6-502">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="145c6-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="145c6-503">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="145c6-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="145c6-504">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="145c6-505">Al usar el modelo de hospedaje fuera de proceso, puede que la aplicación no se apague inmediatamente si hay una conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="145c6-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="145c6-506">Por ejemplo, una conexión WebSocket puede retrasar el apagado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="145c6-507">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="145c6-507">Start-up error page</span></span>

<span data-ttu-id="145c6-508">Tanto el hospedaje en proceso como el hospedaje fuera de proceso generan páginas de error personalizado cuando se produce un error al iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="145c6-509">Si el módulo ASP.NET Core no logra encontrar el controlador de solicitudes en proceso o fuera de proceso, aparecerá la página de código de estado *500.0 - Error de carga de controlador en proceso/fuera de proceso*.</span><span class="sxs-lookup"><span data-stu-id="145c6-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="145c6-510">Para el hospedaje en proceso, si el módulo ASP.NET Core no logra iniciar la aplicación, aparecerá la página de código de estado *500.30 - Error de inicio*.</span><span class="sxs-lookup"><span data-stu-id="145c6-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="145c6-511">En cuanto al hospedaje fuera de proceso, si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparecerá la página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="145c6-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="145c6-512">Para suprimir esta página y volver a la página de código de estado 5xx de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="145c6-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="145c6-513">Para obtener más información sobre cómo configurar los mensajes de error personalizados, consulte [Errores HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="145c6-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="145c6-514">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="145c6-514">Log creation and redirection</span></span>

<span data-ttu-id="145c6-515">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="145c6-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="145c6-516">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="145c6-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="145c6-517">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="145c6-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="145c6-518">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="145c6-519">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="145c6-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="145c6-520">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="145c6-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="145c6-521">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="145c6-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="145c6-522">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="145c6-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="145c6-523">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="145c6-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="145c6-524">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="145c6-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="145c6-525">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="145c6-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="145c6-526">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="145c6-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="145c6-527">Si `stdoutLogEnabled` es falso, los errores que se produzcan al iniciar la aplicación se registrarán y se emitirán en el registro de eventos hasta un máximo de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="145c6-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="145c6-528">Después del inicio, se descartarán los registros adicionales.</span><span class="sxs-lookup"><span data-stu-id="145c6-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="145c6-529">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="145c6-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="145c6-530">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="145c6-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="145c6-531">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="145c6-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="145c6-532">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="145c6-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="145c6-533">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="145c6-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="145c6-534">Registros de diagnóstico mejorados</span><span class="sxs-lookup"><span data-stu-id="145c6-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="145c6-535">El módulo ASP.NET Core es configurable para proporcionar registros de diagnóstico mejorados.</span><span class="sxs-lookup"><span data-stu-id="145c6-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="145c6-536">Agregue el elemento `<handlerSettings>` al elemento `<aspNetCore>` de *web.config*. Al establecer `debugLevel` en `TRACE` se expone una fidelidad mayor de información de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="145c6-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="145c6-537">El módulo no crea automáticamente las carpetas de la ruta de acceso proporcionada al valor `<handlerSetting>` (*logs* en el ejemplo anterior), que deben existir previamente en la implementación.</span><span class="sxs-lookup"><span data-stu-id="145c6-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="145c6-538">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="145c6-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="145c6-539">Los valores de nivel de depuración (`debugLevel`) pueden incluir el nivel y la ubicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="145c6-540">Niveles (en orden de menos a más detallado):</span><span class="sxs-lookup"><span data-stu-id="145c6-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="145c6-541">ERROR</span><span class="sxs-lookup"><span data-stu-id="145c6-541">ERROR</span></span>
* <span data-ttu-id="145c6-542">WARNING</span><span class="sxs-lookup"><span data-stu-id="145c6-542">WARNING</span></span>
* <span data-ttu-id="145c6-543">INFO</span><span class="sxs-lookup"><span data-stu-id="145c6-543">INFO</span></span>
* <span data-ttu-id="145c6-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="145c6-544">TRACE</span></span>

<span data-ttu-id="145c6-545">Ubicaciones (se permiten varias ubicaciones):</span><span class="sxs-lookup"><span data-stu-id="145c6-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="145c6-546">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="145c6-546">CONSOLE</span></span>
* <span data-ttu-id="145c6-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="145c6-547">EVENTLOG</span></span>
* <span data-ttu-id="145c6-548">ARCHIVO</span><span class="sxs-lookup"><span data-stu-id="145c6-548">FILE</span></span>

<span data-ttu-id="145c6-549">También se puede proporcionar la configuración de controlador a través de variables de entorno:</span><span class="sxs-lookup"><span data-stu-id="145c6-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="145c6-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Ruta de acceso al archivo de registro de depuración.</span><span class="sxs-lookup"><span data-stu-id="145c6-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="145c6-551">(El valor predeterminado es *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="145c6-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="145c6-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Valor de nivel de depuración.</span><span class="sxs-lookup"><span data-stu-id="145c6-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="145c6-553">**No** deje habilitado el registro de depuración más tiempo del necesario en la implementación para solucionar un problema.</span><span class="sxs-lookup"><span data-stu-id="145c6-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="145c6-554">El tamaño del registro no es limitado.</span><span class="sxs-lookup"><span data-stu-id="145c6-554">The size of the log isn't limited.</span></span> <span data-ttu-id="145c6-555">Dejar habilitado el registro de depuración puede agotar el espacio disponible en disco y bloquear el servidor o el servicio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="145c6-556">Consulte [Configuración con web.config](#configuration-with-webconfig) para ver un ejemplo del elemento `aspNetCore` en el archivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="145c6-557">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="145c6-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="145c6-558">*Solo se aplica al hospedaje fuera de proceso.*</span><span class="sxs-lookup"><span data-stu-id="145c6-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="145c6-559">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="145c6-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="145c6-560">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="145c6-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="145c6-561">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="145c6-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="145c6-562">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="145c6-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="145c6-563">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="145c6-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="145c6-564">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="145c6-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="145c6-565">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="145c6-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="145c6-566">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="145c6-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="145c6-567">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="145c6-568">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="145c6-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="145c6-569">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="145c6-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="145c6-570">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="145c6-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="145c6-571">Cuando se usa una configuración compartida de IIS en el mismo equipo que la instalación de IIS, ejecute el instalador del lote de hospedaje de ASP.NET Core con el parámetro `OPT_NO_SHARED_CONFIG_CHECK` establecido en `1`:</span><span class="sxs-lookup"><span data-stu-id="145c6-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="145c6-572">Cuando la ruta de acceso a la configuración compartida no se encuentra en el mismo equipo que la instalación de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="145c6-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="145c6-573">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="145c6-574">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="145c6-574">Run the installer.</span></span>
1. <span data-ttu-id="145c6-575">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="145c6-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="145c6-576">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="145c6-577">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="145c6-578">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="145c6-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="145c6-579">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="145c6-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="145c6-580">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="145c6-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="145c6-581">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="145c6-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="145c6-582">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="145c6-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="145c6-583">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="145c6-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="145c6-584">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="145c6-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="145c6-585">Module</span><span class="sxs-lookup"><span data-stu-id="145c6-585">Module</span></span>

<span data-ttu-id="145c6-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="145c6-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="145c6-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="145c6-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="145c6-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="145c6-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="145c6-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="145c6-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="145c6-596">Schema</span><span class="sxs-lookup"><span data-stu-id="145c6-596">Schema</span></span>

<span data-ttu-id="145c6-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="145c6-597">**IIS**</span></span>

* <span data-ttu-id="145c6-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="145c6-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="145c6-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="145c6-600">**IIS Express**</span></span>

* <span data-ttu-id="145c6-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="145c6-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="145c6-603">Configuración</span><span class="sxs-lookup"><span data-stu-id="145c6-603">Configuration</span></span>

<span data-ttu-id="145c6-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="145c6-604">**IIS**</span></span>

* <span data-ttu-id="145c6-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="145c6-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="145c6-606">**IIS Express**</span></span>

* <span data-ttu-id="145c6-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="145c6-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="145c6-609">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="145c6-610">El módulo ASP.NET Core es un módulo nativo de IIS que se conecta a la canalización de IIS para reenviar solicitudes web a aplicaciones ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="145c6-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="145c6-611">Versiones de Windows compatibles:</span><span class="sxs-lookup"><span data-stu-id="145c6-611">Supported Windows versions:</span></span>

* <span data-ttu-id="145c6-612">Windows 7 o posterior</span><span class="sxs-lookup"><span data-stu-id="145c6-612">Windows 7 or later</span></span>
* <span data-ttu-id="145c6-613">Windows Server 2008 R2 o posterior</span><span class="sxs-lookup"><span data-stu-id="145c6-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="145c6-614">El módulo funciona únicamente con Kestrel.</span><span class="sxs-lookup"><span data-stu-id="145c6-614">The module only works with Kestrel.</span></span> <span data-ttu-id="145c6-615">El módulo no es compatible con [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="145c6-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="145c6-616">Dado que las aplicaciones ASP.NET Core se ejecutan en un proceso independiente del proceso de trabajo de IIS, el módulo también se encarga de la administración de procesos.</span><span class="sxs-lookup"><span data-stu-id="145c6-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="145c6-617">El módulo inicia el proceso de la aplicación ASP.NET Core cuando entra la primera solicitud, y reinicia la aplicación si esta se bloquea.</span><span class="sxs-lookup"><span data-stu-id="145c6-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="145c6-618">Este comportamiento es básicamente el mismo que el de las aplicaciones ASP.NET 4.x que se ejecutan en el proceso en IIS y se administran a través del [Servicio de activación de procesos de Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="145c6-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="145c6-619">En el siguiente diagrama se muestra la relación entre IIS, el módulo ASP.NET Core y una aplicación:</span><span class="sxs-lookup"><span data-stu-id="145c6-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="145c6-621">Las solicitudes llegan procedentes de Internet al controlador HTTP.sys en modo kernel.</span><span class="sxs-lookup"><span data-stu-id="145c6-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="145c6-622">El controlador enruta las solicitudes a IIS en el puerto configurado del sitio web, que suele ser el puerto 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="145c6-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="145c6-623">El módulo reenvía las solicitudes a Kestrel en un puerto aleatorio de la aplicación, que no es ni 80 ni 443.</span><span class="sxs-lookup"><span data-stu-id="145c6-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="145c6-624">El módulo especifica el puerto a través de la variable de entorno en el inicio, y el [middleware de integración de IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura el servidor para que escuche en `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="145c6-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="145c6-625">Se realizan más comprobaciones y se rechazan las solicitudes que no se hayan originado en el módulo.</span><span class="sxs-lookup"><span data-stu-id="145c6-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="145c6-626">El módulo no admite el reenvío de HTTPS, por lo que las solicitudes se reenvían a través de HTTP, aunque IIS las reciba a través de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="145c6-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="145c6-627">Una vez que Kestrel toma la solicitud del módulo, la envía a la canalización de middleware de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="145c6-628">La canalización de middleware controla la solicitud y la pasa como una instancia de `HttpContext` a la lógica de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="145c6-629">El middleware agregado por la integración de IIS actualiza el esquema, la dirección IP remota y PathBase para responder del reenvío de la solicitud a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="145c6-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="145c6-630">La respuesta de la aplicación se vuelve a pasar a IIS, que la envía de nuevo al cliente HTTP que inició la solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="145c6-631">Muchos de los módulos nativos, como la autenticación de Windows, permanecen activos.</span><span class="sxs-lookup"><span data-stu-id="145c6-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="145c6-632">Para obtener más información sobre los módulos de IIS activos con el módulo ASP.NET Core, vea <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="145c6-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="145c6-633">El módulo ASP.NET Core también puede:</span><span class="sxs-lookup"><span data-stu-id="145c6-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="145c6-634">Establecer variables de entorno para un proceso de trabajo.</span><span class="sxs-lookup"><span data-stu-id="145c6-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="145c6-635">Registrar la salida en un almacenamiento de archivos para solucionar problemas de inicio.</span><span class="sxs-lookup"><span data-stu-id="145c6-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="145c6-636">Reenviar tokens de autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="145c6-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="145c6-637">Cómo instalar y usar el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="145c6-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="145c6-638">Para obtener instrucciones sobre cómo instalar y usar el módulo ASP.NET Core, consulte [Instalación del conjunto de hospedaje de .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="145c6-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="145c6-639">Configuración con web.config</span><span class="sxs-lookup"><span data-stu-id="145c6-639">Configuration with web.config</span></span>

<span data-ttu-id="145c6-640">El módulo ASP.NET Core se configura con la sección `aspNetCore` del nodo `system.webServer` del archivo *web.config* del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="145c6-641">El siguiente archivo *web.config* se publica para una [implementación dependiente del marco](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) y configura el módulo ASP.NET Core para controlar las solicitudes de sitios:</span><span class="sxs-lookup"><span data-stu-id="145c6-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="145c6-642">El siguiente archivo *web.config* se publica para una [implementación independiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="145c6-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="145c6-643">Cuando se implementa una aplicación en [Azure App Service](https://azure.microsoft.com/services/app-service/), la ruta de acceso de `stdoutLogFile` se establece en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="145c6-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="145c6-644">La ruta de acceso guarda los registros de stdout en la carpeta *LogFiles*, que es una ubicación que el servicio crea automáticamente.</span><span class="sxs-lookup"><span data-stu-id="145c6-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="145c6-645">Para obtener información sobre la configuración de aplicaciones secundarias de IIS, consulte <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="145c6-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="145c6-646">Atributos del elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="145c6-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="145c6-647">Atributo</span><span class="sxs-lookup"><span data-stu-id="145c6-647">Attribute</span></span> | <span data-ttu-id="145c6-648">Descripción</span><span class="sxs-lookup"><span data-stu-id="145c6-648">Description</span></span> | <span data-ttu-id="145c6-649">Default</span><span class="sxs-lookup"><span data-stu-id="145c6-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="145c6-650">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-650">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-651">Argumentos para el archivo ejecutable especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="145c6-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="145c6-652">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-653">Si es true, la página **502.5 - Error en el proceso** se suprime, y tiene prioridad la página de código de estado 502 configurada en *web.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="145c6-654">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-655">Si es true, el token se reenvía al proceso secundario que escucha en % ASPNETCORE_PORT % como un encabezado "MS-ASPNETCORE-WINAUTHTOKEN" por solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="145c6-656">Es responsabilidad de dicho proceso llamar a CloseHandle en este token por solicitud.</span><span class="sxs-lookup"><span data-stu-id="145c6-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="145c6-657">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-658">Especifica el número de instancias del proceso especificado en el valor **processPath** que pueden rotarse por aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="145c6-659">No se recomienda establecer `processesPerApplication`.</span><span class="sxs-lookup"><span data-stu-id="145c6-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="145c6-660">Este atributo se quitará en futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="145c6-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="145c6-661">Valor predeterminado: `1`</span><span class="sxs-lookup"><span data-stu-id="145c6-661">Default: `1`</span></span><br><span data-ttu-id="145c6-662">Mínimo: `1`</span><span class="sxs-lookup"><span data-stu-id="145c6-662">Min: `1`</span></span><br><span data-ttu-id="145c6-663">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="145c6-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="145c6-664">Atributo de cadena necesario.</span><span class="sxs-lookup"><span data-stu-id="145c6-664">Required string attribute.</span></span></p><p><span data-ttu-id="145c6-665">Ruta de acceso al archivo ejecutable que inicia un proceso que escucha las solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="145c6-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="145c6-666">No se admiten rutas de acceso relativas.</span><span class="sxs-lookup"><span data-stu-id="145c6-666">Relative paths are supported.</span></span> <span data-ttu-id="145c6-667">Si la ruta de acceso comienza con `.`, se considera que es relativa a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="145c6-668">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-669">Especifica el número de veces que el proceso indicado en **processPath** puede bloquearse por minuto.</span><span class="sxs-lookup"><span data-stu-id="145c6-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="145c6-670">Si se supera este límite, el módulo deja de iniciar el proceso durante lo que resta del minuto.</span><span class="sxs-lookup"><span data-stu-id="145c6-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="145c6-671">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="145c6-671">Default: `10`</span></span><br><span data-ttu-id="145c6-672">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-672">Min: `0`</span></span><br><span data-ttu-id="145c6-673">Máximo: `100`</span><span class="sxs-lookup"><span data-stu-id="145c6-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="145c6-674">Atributo timespan opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="145c6-675">Especifica el tiempo que el módulo ASP.NET Core espera una respuesta del proceso que escucha en % ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="145c6-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="145c6-676">En las versiones del módulo ASP.NET Core que se envían con la versión de ASP.NET Core 2.1 o posterior, el valor `requestTimeout` se especifica en horas, minutos y segundos.</span><span class="sxs-lookup"><span data-stu-id="145c6-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="145c6-677">Valor predeterminado: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-677">Default: `00:02:00`</span></span><br><span data-ttu-id="145c6-678">Mínimo: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-678">Min: `00:00:00`</span></span><br><span data-ttu-id="145c6-679">Máximo: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="145c6-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="145c6-680">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-681">Tiempo en segundos que el módulo espera a que se cierre correctamente el archivo ejecutable cuando se detecta el archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="145c6-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="145c6-682">Valor predeterminado: `10`</span><span class="sxs-lookup"><span data-stu-id="145c6-682">Default: `10`</span></span><br><span data-ttu-id="145c6-683">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-683">Min: `0`</span></span><br><span data-ttu-id="145c6-684">Máximo: `600`</span><span class="sxs-lookup"><span data-stu-id="145c6-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="145c6-685">Atributo integer opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="145c6-686">Tiempo en segundos que espera el módulo a que el archivo ejecutable inicie u proceso que escucha en el puerto.</span><span class="sxs-lookup"><span data-stu-id="145c6-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="145c6-687">Si se supera este límite de tiempo, el módulo termina el proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="145c6-688">El módulo intenta reiniciar el proceso cuando se recibe una nueva solicitud y lo sigue intentando en las sucesivas solicitudes entrantes a no ser que la aplicación no pueda iniciar **rapidFailsPerMinute** un número de veces en el último minuto acumulado.</span><span class="sxs-lookup"><span data-stu-id="145c6-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="145c6-689">Un valor de 0 (cero) **no** se considera un tiempo de expiración infinito.</span><span class="sxs-lookup"><span data-stu-id="145c6-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="145c6-690">Valor predeterminado: `120`</span><span class="sxs-lookup"><span data-stu-id="145c6-690">Default: `120`</span></span><br><span data-ttu-id="145c6-691">Mínimo: `0`</span><span class="sxs-lookup"><span data-stu-id="145c6-691">Min: `0`</span></span><br><span data-ttu-id="145c6-692">Máximo: `3600`</span><span class="sxs-lookup"><span data-stu-id="145c6-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="145c6-693">Atributo Boolean opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="145c6-694">Si es true, **stdout** y **stderr** en el proceso especificado en **processPath** se redirigen al archivo especificado en **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="145c6-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="145c6-695">Atributo de cadena opcional.</span><span class="sxs-lookup"><span data-stu-id="145c6-695">Optional string attribute.</span></span></p><p><span data-ttu-id="145c6-696">Especifica la ruta de acceso relativa o absoluta para la que se registran **stdout** y **stderr** desde el proceso especificado en **processPath**.</span><span class="sxs-lookup"><span data-stu-id="145c6-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="145c6-697">Las rutas de acceso relativas son relativas a la raíz del sitio.</span><span class="sxs-lookup"><span data-stu-id="145c6-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="145c6-698">Cualquier ruta de acceso que se inicia con `.` es relativa a la raíz del sitio y todas las demás rutas de acceso se tratan como absolutas.</span><span class="sxs-lookup"><span data-stu-id="145c6-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="145c6-699">Las carpetas que se proporcionan en la ruta de acceso deben estar en orden para que el módulo cree el archivo de registro.</span><span class="sxs-lookup"><span data-stu-id="145c6-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="145c6-700">Mediante delimitadores se agrega una marca de tiempo, un identificador de proceso y una extensión de archivo ( *.log*) al último segmento de la ruta de acceso **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="145c6-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="145c6-701">Si `.\logs\stdout` se proporciona como valor, se guarda un registro de ejemplo de stdout como *stdout_20180205194132_1934.log* en la carpeta *logs*, cuando se guarda el 5/2/2018 a las 19:41:32 con un identificador de proceso de 1934.</span><span class="sxs-lookup"><span data-stu-id="145c6-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="145c6-702">Configuración de las variables de entorno</span><span class="sxs-lookup"><span data-stu-id="145c6-702">Setting environment variables</span></span>

<span data-ttu-id="145c6-703">Se pueden especificar variables de entorno para el proceso en el atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="145c6-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="145c6-704">Especifique una variable de entorno con el elemento secundario `<environmentVariable>` de un elemento de la colección `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="145c6-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="145c6-705">Las variables de entorno establecidas en esta sección entran en conflicto con las variables de entorno del sistema que tienen el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="145c6-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="145c6-706">Si se establece una variable de entorno en el archivo *web.config* y en el nivel del sistema en Windows, el valor del archivo *web.config* se anexa al valor de la variable de entorno del sistema (por ejemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), que impide que se inicie la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="145c6-707">En el ejemplo siguiente se establecen dos variables de entorno.</span><span class="sxs-lookup"><span data-stu-id="145c6-707">The following example sets two environment variables.</span></span> <span data-ttu-id="145c6-708">`ASPNETCORE_ENVIRONMENT` configura el entorno de la aplicación como `Development`.</span><span class="sxs-lookup"><span data-stu-id="145c6-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="145c6-709">Un desarrollador puede establecer temporalmente este valor en el archivo *web.config* con el fin de forzar a que se cargue la [página de excepciones del desarrollador](xref:fundamentals/error-handling) al depurar una excepción de aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="145c6-710">`CONFIG_DIR` es un ejemplo de una variable de entorno definida por el usuario, donde el desarrollador ha escrito código que lee el valor al inicio para formar una ruta de acceso destinada a la carga del archivo de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="145c6-711">Establezca solo la variable de entorno `ASPNETCORE_ENVIRONMENT` en `Development` en servidores de ensayo y pruebas a los que no puedan acceder redes que no son de confianza, como Internet.</span><span class="sxs-lookup"><span data-stu-id="145c6-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="145c6-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="145c6-712">app_offline.htm</span></span>

<span data-ttu-id="145c6-713">Si un archivo con el nombre *app_offline.htm* se detecta en el directorio raíz de una aplicación, el módulo ASP.NET Core intenta cerrar correctamente la aplicación y deja de procesar las solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="145c6-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="145c6-714">Si la aplicación se sigue ejecutando después del número definido en `shutdownTimeLimit`, el módulo ASP.NET Core termina el proceso en ejecución.</span><span class="sxs-lookup"><span data-stu-id="145c6-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="145c6-715">Mientras el archivo *app_offline.htm* existe, el módulo ASP.NET Core responde a solicitudes con la devolución del contenido del archivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="145c6-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="145c6-716">Cuando se quita el archivo *app_offline.htm*, la solicitud siguiente inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="145c6-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="145c6-717">Página de errores de inicio</span><span class="sxs-lookup"><span data-stu-id="145c6-717">Start-up error page</span></span>

<span data-ttu-id="145c6-718">Si el módulo ASP.NET Core no es capaz de iniciar el proceso de back-end o este se inicia pero no puede escuchar en el puerto configurado, aparece una página de código de estado *502.5 - Error de proceso*.</span><span class="sxs-lookup"><span data-stu-id="145c6-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="145c6-719">Para suprimir esta página y volver a la página de código de estado 502 de IIS predeterminada, use el atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="145c6-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="145c6-720">Para obtener más información sobre cómo configurar los mensajes de error personalizados, consulte [Errores HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="145c6-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de códigos de estado 502.5 Error de proceso](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="145c6-722">Creación y redireccionamiento de registros</span><span class="sxs-lookup"><span data-stu-id="145c6-722">Log creation and redirection</span></span>

<span data-ttu-id="145c6-723">El módulo ASP.NET Core redirige los resultados de consola stdout y stderr al disco si se establecen los atributos `stdoutLogEnabled` y `stdoutLogFile` del elemento `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="145c6-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="145c6-724">Al crearse el archivo de registro, el módulo crea las carpetas de la ruta de acceso de `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="145c6-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="145c6-725">El grupo de aplicaciones debe tener acceso de escritura a la ubicación en la que se escriben los registros (use `IIS AppPool\<app_pool_name>` para proporcionar permiso de escritura).</span><span class="sxs-lookup"><span data-stu-id="145c6-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="145c6-726">Los registros no se rotan, a no ser que se produzca un reinicio o reciclaje del proceso.</span><span class="sxs-lookup"><span data-stu-id="145c6-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="145c6-727">Es responsabilidad del proveedor de servicios de hospedaje limitar el espacio en disco que consumen los registros.</span><span class="sxs-lookup"><span data-stu-id="145c6-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="145c6-728">Solo se recomienda usar el registro stdout para solucionar problemas de inicio de la aplicación al hospedar en IIS o al usar [compatibilidad de IIS en tiempo de desarrollo para Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), no durante la depuración local y ejecución de la aplicación con IIS Express.</span><span class="sxs-lookup"><span data-stu-id="145c6-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="145c6-729">No use el registro de stdout con fines de registro de aplicaciones general.</span><span class="sxs-lookup"><span data-stu-id="145c6-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="145c6-730">Para el registro rutinario en una aplicación ASP.NET Core, use una biblioteca de registro que limite el tamaño del archivo de registro y realice la rotación de los registros.</span><span class="sxs-lookup"><span data-stu-id="145c6-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="145c6-731">Para más información, consulte los [proveedores de registro de terceros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="145c6-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="145c6-732">Cuando se crea el archivo de registro, se agregan automáticamente una marca de tiempo y una extensión de archivo.</span><span class="sxs-lookup"><span data-stu-id="145c6-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="145c6-733">El nombre del archivo de registro se forma mediante la anexión de la marca de tiempo, el identificador de proceso y la extensión de archivo ( *.log*) al último segmento de la ruta de acceso `stdoutLogFile` (normalmente *stdout*) delimitados por caracteres de subrayado.</span><span class="sxs-lookup"><span data-stu-id="145c6-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="145c6-734">Si la ruta de acceso de `stdoutLogFile` finaliza con *stdout*, el registro de una aplicación con un PID de 1934 creado el 5/2/2018 a las 19:42:32 tiene el nombre de archivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="145c6-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="145c6-735">En el siguiente elemento `aspNetCore` de ejemplo se configura el registro de stdout en la ruta de acceso relativa `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="145c6-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="145c6-736">Confirme que la identidad del usuario de AppPool tenga permiso para escribir en la ruta de acceso proporcionada.</span><span class="sxs-lookup"><span data-stu-id="145c6-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="145c6-737">Al publicar una aplicación para la implementación de Azure App Service, el SDK web establece el valor de `stdoutLogFile` en `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="145c6-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="145c6-738">La variable de entorno `%home` está predefinida para las aplicaciones hospedadas por Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="145c6-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="145c6-739">Para crear reglas de filtro de registro, vea las secciones [Configuración](xref:fundamentals/logging/index#log-filtering) y [Filtrado de registros](xref:fundamentals/logging/index#log-filtering) de la documentación del registro en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="145c6-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="145c6-740">Para más información sobre los formatos de ruta de acceso, vea [Formatos de ruta de acceso de archivo en los sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="145c6-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="145c6-741">La configuración de proxy usa el protocolo HTTP y un token de emparejamiento</span><span class="sxs-lookup"><span data-stu-id="145c6-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="145c6-742">El proxy creado entre el módulo ASP.NET Core y Kestrel usa el protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="145c6-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="145c6-743">No hay ningún riesgo de interceptación del tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="145c6-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="145c6-744">Un token de emparejamiento sirve para garantizar que las solicitudes recibidas por Kestrel se redirigieron mediante proxy por IIS y no procedieron de otra fuente.</span><span class="sxs-lookup"><span data-stu-id="145c6-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="145c6-745">El módulo crea el token de emparejamiento y lo establece en una variable de entorno (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="145c6-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="145c6-746">El token de emparejamiento también se establece en un encabezado (`MS-ASPNETCORE-TOKEN`) en cada solicitud redirigida mediante proxy.</span><span class="sxs-lookup"><span data-stu-id="145c6-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="145c6-747">El middleware de IIS comprueba cada solicitud recibida para confirmar que el valor del encabezado del token de emparejamiento coincida con el valor de la variable de entorno.</span><span class="sxs-lookup"><span data-stu-id="145c6-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="145c6-748">Si los valores del token no coinciden, la solicitud se registra y se rechaza.</span><span class="sxs-lookup"><span data-stu-id="145c6-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="145c6-749">No se puede acceder a la variable de entorno del token de emparejamiento y al tráfico entre el módulo y Kestrel desde una ubicación fuera del servidor.</span><span class="sxs-lookup"><span data-stu-id="145c6-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="145c6-750">Sin conocer el valor del token de emparejamiento, un atacante no puede enviar solicitudes que omitan la comprobación en el middleware de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="145c6-751">El módulo ASP.NET Core con una configuración compartida de IIS</span><span class="sxs-lookup"><span data-stu-id="145c6-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="145c6-752">El instalador del módulo ASP.NET Core se ejecuta con los privilegios de la cuenta **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="145c6-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="145c6-753">Como la cuenta local del sistema no tiene permiso de modificación en la ruta de acceso de recurso compartido que se usa en la configuración compartida de IIS, el instalador inicia un error de acceso denegado al intentar configurar los valores del módulo en el archivo *applicationHost.config* del recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="145c6-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="145c6-754">Al usar una configuración compartida de IIS, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="145c6-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="145c6-755">Deshabilite la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="145c6-756">Ejecute el instalador.</span><span class="sxs-lookup"><span data-stu-id="145c6-756">Run the installer.</span></span>
1. <span data-ttu-id="145c6-757">Exporte el archivo *applicationHost.config* actualizado al recurso compartido.</span><span class="sxs-lookup"><span data-stu-id="145c6-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="145c6-758">Vuelva a habilitar la configuración compartida de IIS.</span><span class="sxs-lookup"><span data-stu-id="145c6-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="145c6-759">Versión del módulo y registros del instalador de la agrupación de hospedaje</span><span class="sxs-lookup"><span data-stu-id="145c6-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="145c6-760">Para determinar la versión instalada del módulo ASP.NET Core, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="145c6-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="145c6-761">En el sistema de hospedaje, vaya a *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="145c6-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="145c6-762">Busque el archivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="145c6-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="145c6-763">Haga clic con el botón derecho en el archivo y seleccione **Propiedades** en el menú contextual.</span><span class="sxs-lookup"><span data-stu-id="145c6-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="145c6-764">Seleccione la pestaña **Detalles**. La **versión del archivo** y la **versión del producto** representan la versión instalada del módulo.</span><span class="sxs-lookup"><span data-stu-id="145c6-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="145c6-765">Los registros del instalador de la agrupación de hospedaje del módulo se encuentran en *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. El archivo se llama *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="145c6-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="145c6-766">Ubicaciones del módulo, el esquema y el archivo de configuración</span><span class="sxs-lookup"><span data-stu-id="145c6-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="145c6-767">Module</span><span class="sxs-lookup"><span data-stu-id="145c6-767">Module</span></span>

<span data-ttu-id="145c6-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="145c6-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="145c6-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="145c6-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="145c6-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="145c6-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="145c6-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="145c6-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="145c6-774">Schema</span><span class="sxs-lookup"><span data-stu-id="145c6-774">Schema</span></span>

<span data-ttu-id="145c6-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="145c6-775">**IIS**</span></span>

* <span data-ttu-id="145c6-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="145c6-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="145c6-777">**IIS Express**</span></span>

* <span data-ttu-id="145c6-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="145c6-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="145c6-779">Configuración</span><span class="sxs-lookup"><span data-stu-id="145c6-779">Configuration</span></span>

<span data-ttu-id="145c6-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="145c6-780">**IIS**</span></span>

* <span data-ttu-id="145c6-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="145c6-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="145c6-782">**IIS Express**</span></span>

* <span data-ttu-id="145c6-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="145c6-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="145c6-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="145c6-785">Los archivos se pueden encontrar mediante la búsqueda de *aspnetcore* en el archivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="145c6-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="145c6-786">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="145c6-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="145c6-787">[Origen de la referencia del módulo de ASP.NET Core (rama maestra)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use la lista desplegable **Rama** para seleccionar una versión específica (por ejemplo, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="145c6-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
