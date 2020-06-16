---
title: Configuración del modelo de hospedaje de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones de Razor Pages y MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 74501c70df5ad33d5a2478b2ec359713e29292d8
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755786"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="33c0d-103">Configuración del modelo de hospedaje de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33c0d-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="33c0d-104">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="33c0d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="33c0d-105">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="33c0d-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="33c0d-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="33c0d-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="33c0d-107">Entorno</span><span class="sxs-lookup"><span data-stu-id="33c0d-107">Environment</span></span>

<span data-ttu-id="33c0d-108">Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="33c0d-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="33c0d-109">Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.</span><span class="sxs-lookup"><span data-stu-id="33c0d-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="33c0d-110">Una aplicación Blazor WebAssembly hospedada selecciona el entorno desde el servidor a través de un middleware que comunica el entorno con el explorador mediante la adición del encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="33c0d-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="33c0d-111">El valor del encabezado es el entorno.</span><span class="sxs-lookup"><span data-stu-id="33c0d-111">The value of the header is the environment.</span></span> <span data-ttu-id="33c0d-112">La aplicación Blazor hospedada y la aplicación de servidor comparten el mismo entorno.</span><span class="sxs-lookup"><span data-stu-id="33c0d-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="33c0d-113">Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="33c0d-114">En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="33c0d-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="33c0d-115">Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="33c0d-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="33c0d-116">En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo *web.config* publicado.</span><span class="sxs-lookup"><span data-stu-id="33c0d-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="33c0d-117">El archivo *web.config* se encuentra en la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish*:</span><span class="sxs-lookup"><span data-stu-id="33c0d-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="33c0d-118">Si quiere usar un archivo *web.config* personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta *publish*, consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="33c0d-119">Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:</span><span class="sxs-lookup"><span data-stu-id="33c0d-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="33c0d-120">Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone el <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:</span><span class="sxs-lookup"><span data-stu-id="33c0d-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="33c0d-121">Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:</span><span class="sxs-lookup"><span data-stu-id="33c0d-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="33c0d-122">La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se puede usar durante el inicio cuando el servicio <xref:Microsoft.AspNetCore.Components.NavigationManager> no está disponible.</span><span class="sxs-lookup"><span data-stu-id="33c0d-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="33c0d-123">Configuración</span><span class="sxs-lookup"><span data-stu-id="33c0d-123">Configuration</span></span>

Blazor<span data-ttu-id="33c0d-124"> WebAssembly carga la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="33c0d-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="33c0d-125">Archivos de configuración de aplicaciones de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="33c0d-125">App settings files by default:</span></span>
  * <span data-ttu-id="33c0d-126">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="33c0d-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="33c0d-127">*wwwroot/appsettings.{ENTORNO}.json*</span><span class="sxs-lookup"><span data-stu-id="33c0d-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="33c0d-128">Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación</span><span class="sxs-lookup"><span data-stu-id="33c0d-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="33c0d-129">No todos los proveedores son adecuados para las aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="33c0d-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="33c0d-130">En [Explicación de los proveedores de configuración de Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134), se explican los proveedores que se admiten en Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="33c0d-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="33c0d-131">La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="33c0d-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="33c0d-132">**No almacene credenciales ni secretos de aplicación en la configuración.**</span><span class="sxs-lookup"><span data-stu-id="33c0d-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="33c0d-133">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="33c0d-134">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="33c0d-134">App settings configuration</span></span>

<span data-ttu-id="33c0d-135">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="33c0d-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="33c0d-136">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="33c0d-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="33c0d-137">Configuración de proveedor</span><span class="sxs-lookup"><span data-stu-id="33c0d-137">Provider configuration</span></span>

<span data-ttu-id="33c0d-138">En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="33c0d-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="33c0d-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="33c0d-139">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

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

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="33c0d-140">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="33c0d-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="33c0d-141">Para leer otros archivos de configuración de la carpeta *wwwroot* en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="33c0d-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="33c0d-142">Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="33c0d-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="33c0d-143">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="33c0d-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="33c0d-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="33c0d-144">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="33c0d-145">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="33c0d-145">Authentication configuration</span></span>

<span data-ttu-id="33c0d-146">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="33c0d-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="33c0d-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="33c0d-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="33c0d-148">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="33c0d-148">Logging configuration</span></span>

<span data-ttu-id="33c0d-149">Agregue una referencia de paquete para [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="33c0d-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="33c0d-150">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="33c0d-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="33c0d-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="33c0d-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="33c0d-152">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="33c0d-152">Host builder configuration</span></span>

<span data-ttu-id="33c0d-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="33c0d-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="33c0d-154">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="33c0d-154">Cached configuration</span></span>

<span data-ttu-id="33c0d-155">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="33c0d-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="33c0d-156">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="33c0d-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="33c0d-157">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="33c0d-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="33c0d-158">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="33c0d-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="33c0d-159">Los archivos *service-worker.js* y *service-worker-assets.js* de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="33c0d-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="33c0d-160">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="33c0d-161">Registro</span><span class="sxs-lookup"><span data-stu-id="33c0d-161">Logging</span></span>

<span data-ttu-id="33c0d-162">Para información sobre la compatibilidad con el registro de Blazor WebAssembly, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="33c0d-163">Negociación entre orígenes de SignalR para la autenticación</span><span class="sxs-lookup"><span data-stu-id="33c0d-163">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="33c0d-164">Para configurar el cliente subyacente de SignalR para que envíe credenciales, como cookies o encabezados de autenticación HTTP:</span><span class="sxs-lookup"><span data-stu-id="33c0d-164">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="33c0d-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para establecer <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> en solicitudes de [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) entre orígenes:</span><span class="sxs-lookup"><span data-stu-id="33c0d-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="33c0d-166">Asigne <xref:System.Net.Http.HttpMessageHandler> a la opción <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="33c0d-166">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="33c0d-167">Para obtener más información, vea <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-167">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="33c0d-168">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="33c0d-168">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="33c0d-169">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="33c0d-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="33c0d-170">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="33c0d-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="33c0d-171">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="33c0d-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="33c0d-172">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="33c0d-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="33c0d-173">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="33c0d-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="33c0d-174">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="33c0d-174">CSS class</span></span>                       | <span data-ttu-id="33c0d-175">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="33c0d-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="33c0d-176">Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="33c0d-176">A lost connection.</span></span> <span data-ttu-id="33c0d-177">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="33c0d-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="33c0d-178">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="33c0d-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="33c0d-179">Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="33c0d-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="33c0d-180">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="33c0d-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="33c0d-181">Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="33c0d-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="33c0d-182">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="33c0d-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="33c0d-183">Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="33c0d-183">Reconnection rejected.</span></span> <span data-ttu-id="33c0d-184">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="33c0d-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="33c0d-185">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="33c0d-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="33c0d-186">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="33c0d-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="33c0d-187">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="33c0d-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="33c0d-188">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="33c0d-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="33c0d-189">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="33c0d-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="33c0d-190">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="33c0d-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="33c0d-191">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="33c0d-191">Render mode</span></span>

<span data-ttu-id="33c0d-192">Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="33c0d-192">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="33c0d-193">Esto se configura en la página *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="33c0d-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="33c0d-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="33c0d-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="33c0d-195">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="33c0d-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="33c0d-196">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="33c0d-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="33c0d-197">Descripción</span><span class="sxs-lookup"><span data-stu-id="33c0d-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="33c0d-198">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="33c0d-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="33c0d-199">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="33c0d-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="33c0d-200">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="33c0d-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="33c0d-201">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="33c0d-201">Output from the component isn't included.</span></span> <span data-ttu-id="33c0d-202">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="33c0d-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="33c0d-203">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="33c0d-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="33c0d-204">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="33c0d-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="33c0d-205">Configuración del cliente de SignalR para aplicaciones de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="33c0d-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="33c0d-206">En ocasiones tendrá que configurar el cliente de SignalR que usan las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="33c0d-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="33c0d-207">Por ejemplo, es posible que quiera configurar el registro en el cliente de SignalR para diagnosticar un problema de conexión.</span><span class="sxs-lookup"><span data-stu-id="33c0d-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="33c0d-208">Para configurar el cliente de SignalR en el archivo *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="33c0d-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="33c0d-209">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="33c0d-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="33c0d-210">Llame a `Blazor.start` y pase un objeto de configuración que especifique el generador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="33c0d-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="33c0d-211">Registro</span><span class="sxs-lookup"><span data-stu-id="33c0d-211">Logging</span></span>

<span data-ttu-id="33c0d-212">Para más información sobre la compatibilidad de registro de servidor de Blazor, vea <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="33c0d-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
