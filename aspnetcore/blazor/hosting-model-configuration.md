---
title: Configuración del modelo de hospedaje de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159624"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="b6df8-103">Configuración del modelo de hospedaje de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6df8-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="b6df8-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b6df8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b6df8-105">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="b6df8-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="b6df8-106">WebAssembly de Blazor</span><span class="sxs-lookup"><span data-stu-id="b6df8-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="b6df8-107">Entorno</span><span class="sxs-lookup"><span data-stu-id="b6df8-107">Environment</span></span>

<span data-ttu-id="b6df8-108">Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b6df8-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="b6df8-109">Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.</span><span class="sxs-lookup"><span data-stu-id="b6df8-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="b6df8-110">Una aplicación WebAssembly de Blazor hospedada selecciona el entorno desde el servidor a través de un middleware agrega el encabezado `blazor-environment` para comunicar el entorno con el explorador.</span><span class="sxs-lookup"><span data-stu-id="b6df8-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="b6df8-111">El valor del encabezado es el entorno.</span><span class="sxs-lookup"><span data-stu-id="b6df8-111">The value of the header is the environment.</span></span> <span data-ttu-id="b6df8-112">La aplicación de Blazor hospedada y la aplicación de servidor comparten el mismo entorno.</span><span class="sxs-lookup"><span data-stu-id="b6df8-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="b6df8-113">Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b6df8-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b6df8-114">En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="b6df8-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="b6df8-115">Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="b6df8-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="b6df8-116">En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo *web.config* publicado.</span><span class="sxs-lookup"><span data-stu-id="b6df8-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="b6df8-117">El archivo *web.config* se encuentra en la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="b6df8-118">Si quiere usar un archivo *web.config* personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta *publish*, consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="b6df8-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="b6df8-119">Para obtener el entorno de la aplicación en un componente, inserte `IWebAssemblyHostEnvironment` y lea la propiedad `Environment`:</span><span class="sxs-lookup"><span data-stu-id="b6df8-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="b6df8-120">Durante el inicio, `WebAssemblyHostBuilder` expone el `IWebAssemblyHostEnvironment` a través de la propiedad `HostEnvironment`, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:</span><span class="sxs-lookup"><span data-stu-id="b6df8-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="b6df8-121">Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:</span><span class="sxs-lookup"><span data-stu-id="b6df8-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="b6df8-122">\`IsEnvironment("{NOMBRE DE ENTORNO}")</span><span class="sxs-lookup"><span data-stu-id="b6df8-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="b6df8-123">La propiedad `IWebAssemblyHostEnvironment.BaseAddress` se puede usar durante el inicio cuando el servicio `NavigationManager` no está disponible.</span><span class="sxs-lookup"><span data-stu-id="b6df8-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="b6df8-124">Configuración</span><span class="sxs-lookup"><span data-stu-id="b6df8-124">Configuration</span></span>

<span data-ttu-id="b6df8-125">WebAssembly de Blazor admite configuraciones procedentes de:</span><span class="sxs-lookup"><span data-stu-id="b6df8-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="b6df8-126">El [proveedor de configuración de archivos](xref:fundamentals/configuration/index#file-configuration-provider) de archivos de configuración de la aplicación de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="b6df8-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="b6df8-127">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b6df8-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="b6df8-128">*wwwroot/appsettings.{ENTORNO}.json*</span><span class="sxs-lookup"><span data-stu-id="b6df8-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="b6df8-129">Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación</span><span class="sxs-lookup"><span data-stu-id="b6df8-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b6df8-130">La configuración de una aplicación de Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="b6df8-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="b6df8-131">**No almacene credenciales ni secretos de aplicación en la configuración.**</span><span class="sxs-lookup"><span data-stu-id="b6df8-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="b6df8-132">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b6df8-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="b6df8-133">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="b6df8-133">App settings configuration</span></span>

<span data-ttu-id="b6df8-134">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="b6df8-135">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="b6df8-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="b6df8-136">Configuración de proveedor</span><span class="sxs-lookup"><span data-stu-id="b6df8-136">Provider configuration</span></span>

<span data-ttu-id="b6df8-137">En el siguiente ejemplo se usa un elemento <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> y el [proveedor de configuración de archivos](xref:fundamentals/configuration/index#file-configuration-provider) para proporcionar una configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="b6df8-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="b6df8-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6df8-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="b6df8-139">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="b6df8-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="b6df8-140">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="b6df8-140">Authentication configuration</span></span>

<span data-ttu-id="b6df8-141">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="b6df8-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6df8-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="b6df8-143">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="b6df8-143">Logging configuration</span></span>

<span data-ttu-id="b6df8-144">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-144">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="b6df8-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6df8-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="b6df8-146">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="b6df8-146">Host builder configuration</span></span>

<span data-ttu-id="b6df8-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6df8-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="b6df8-148">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="b6df8-148">Cached configuration</span></span>

<span data-ttu-id="b6df8-149">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="b6df8-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="b6df8-150">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="b6df8-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="b6df8-151">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b6df8-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="b6df8-152">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="b6df8-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="b6df8-153">Los archivos *service-worker.js* y *service-worker-assets.js* de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="b6df8-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="b6df8-154">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="b6df8-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="b6df8-155">Registro</span><span class="sxs-lookup"><span data-stu-id="b6df8-155">Logging</span></span>

<span data-ttu-id="b6df8-156">Para información sobre la compatibilidad con el registro de WebAssembly de Blazor, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="b6df8-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="b6df8-157">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="b6df8-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="b6df8-158">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="b6df8-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="b6df8-159">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="b6df8-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="b6df8-160">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="b6df8-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="b6df8-161">Para personalizar la interfaz de usuario, defina un elemento con un valor `id` de `components-reconnect-modal` en el elemento `<body>` de la página de Razor *_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="b6df8-162">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="b6df8-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="b6df8-163">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="b6df8-163">CSS class</span></span>                       | <span data-ttu-id="b6df8-164">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="b6df8-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="b6df8-165">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="b6df8-165">A lost connection.</span></span> <span data-ttu-id="b6df8-166">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="b6df8-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="b6df8-167">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="b6df8-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="b6df8-168">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="b6df8-169">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="b6df8-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="b6df8-170">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="b6df8-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="b6df8-171">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="b6df8-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="b6df8-172">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="b6df8-172">Reconnection rejected.</span></span> <span data-ttu-id="b6df8-173">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="b6df8-174">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="b6df8-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="b6df8-175">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="b6df8-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="b6df8-176">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="b6df8-177">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="b6df8-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="b6df8-178">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="b6df8-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="b6df8-179">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b6df8-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="b6df8-180">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="b6df8-180">Render mode</span></span>

<span data-ttu-id="b6df8-181">Las aplicaciones de servidor Blazor se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión de cliente con el servidor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="b6df8-182">Esto se configura en la página de Razor *_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="b6df8-183">`RenderMode` configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="b6df8-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="b6df8-184">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="b6df8-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="b6df8-185">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="b6df8-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="b6df8-186">Descripción</span><span class="sxs-lookup"><span data-stu-id="b6df8-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="b6df8-187">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b6df8-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b6df8-188">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="b6df8-189">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b6df8-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b6df8-190">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="b6df8-190">Output from the component isn't included.</span></span> <span data-ttu-id="b6df8-191">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="b6df8-192">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="b6df8-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="b6df8-193">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="b6df8-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="b6df8-194">Configuración del cliente de SignalR para aplicaciones de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="b6df8-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="b6df8-195">En ocasiones tendrá que configurar el cliente de SignalR que usan las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="b6df8-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="b6df8-196">Por ejemplo, es posible que quiera configurar el registro en el cliente de SignalR para diagnosticar un problema de conexión.</span><span class="sxs-lookup"><span data-stu-id="b6df8-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="b6df8-197">Para configurar el cliente de SignalR en el archivo *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b6df8-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="b6df8-198">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="b6df8-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b6df8-199">Llame a `Blazor.start` y pase un objeto de configuración que especifique el generador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="b6df8-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="b6df8-200">Registro</span><span class="sxs-lookup"><span data-stu-id="b6df8-200">Logging</span></span>

<span data-ttu-id="b6df8-201">Para más información sobre la compatibilidad de registro de servidor de Blazor, vea <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="b6df8-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
