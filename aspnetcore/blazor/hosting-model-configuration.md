---
<span data-ttu-id="397c3-101">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-103">'Blazor'</span></span>
- <span data-ttu-id="397c3-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-104">'Identity'</span></span>
- <span data-ttu-id="397c3-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-106">'Razor'</span></span>
- <span data-ttu-id="397c3-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="397c3-108">Configuración del modelo de hospedaje de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="397c3-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="397c3-109">Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="397c3-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="397c3-110">En este artículo se describe la configuración del modelo de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="397c3-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="397c3-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="397c3-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="397c3-112">Entorno</span><span class="sxs-lookup"><span data-stu-id="397c3-112">Environment</span></span>

<span data-ttu-id="397c3-113">Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="397c3-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="397c3-114">Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.</span><span class="sxs-lookup"><span data-stu-id="397c3-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="397c3-115">Una aplicación Blazor WebAssembly hospedada selecciona el entorno desde el servidor a través de un middleware que comunica el entorno con el explorador mediante la adición del encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="397c3-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="397c3-116">El valor del encabezado es el entorno.</span><span class="sxs-lookup"><span data-stu-id="397c3-116">The value of the header is the environment.</span></span> <span data-ttu-id="397c3-117">La aplicación Blazor hospedada y la aplicación de servidor comparten el mismo entorno.</span><span class="sxs-lookup"><span data-stu-id="397c3-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="397c3-118">Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="397c3-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="397c3-119">En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo.</span><span class="sxs-lookup"><span data-stu-id="397c3-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="397c3-120">Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.</span><span class="sxs-lookup"><span data-stu-id="397c3-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="397c3-121">En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo *web.config* publicado.</span><span class="sxs-lookup"><span data-stu-id="397c3-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="397c3-122">El archivo *web.config* se encuentra en la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish*:</span><span class="sxs-lookup"><span data-stu-id="397c3-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="397c3-123">Si quiere usar un archivo *web.config* personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta *publish*, consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="397c3-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="397c3-124">Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:</span><span class="sxs-lookup"><span data-stu-id="397c3-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="397c3-125">Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone el <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:</span><span class="sxs-lookup"><span data-stu-id="397c3-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="397c3-126">Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:</span><span class="sxs-lookup"><span data-stu-id="397c3-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="397c3-127">La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se puede usar durante el inicio cuando el servicio <xref:Microsoft.AspNetCore.Components.NavigationManager> no está disponible.</span><span class="sxs-lookup"><span data-stu-id="397c3-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="397c3-128">Configuración</span><span class="sxs-lookup"><span data-stu-id="397c3-128">Configuration</span></span>

Blazor<span data-ttu-id="397c3-129"> WebAssembly carga la configuración desde:</span><span class="sxs-lookup"><span data-stu-id="397c3-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="397c3-130">Archivos de configuración de aplicaciones de forma predeterminada:</span><span class="sxs-lookup"><span data-stu-id="397c3-130">App settings files by default:</span></span>
  * <span data-ttu-id="397c3-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="397c3-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="397c3-132">*wwwroot/appsettings.{ENTORNO}.json*</span><span class="sxs-lookup"><span data-stu-id="397c3-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="397c3-133">Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación</span><span class="sxs-lookup"><span data-stu-id="397c3-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="397c3-134">No todos los proveedores son adecuados para las aplicaciones Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="397c3-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="397c3-135">En [Explicación de los proveedores de configuración de Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134), se explican los proveedores que se admiten en Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="397c3-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="397c3-136">La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.</span><span class="sxs-lookup"><span data-stu-id="397c3-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="397c3-137">**No almacene credenciales ni secretos de aplicación en la configuración.**</span><span class="sxs-lookup"><span data-stu-id="397c3-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="397c3-138">Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="397c3-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="397c3-139">Configuración de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="397c3-139">App settings configuration</span></span>

<span data-ttu-id="397c3-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="397c3-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="397c3-141">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="397c3-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="397c3-142">Configuración de proveedor</span><span class="sxs-lookup"><span data-stu-id="397c3-142">Provider configuration</span></span>

<span data-ttu-id="397c3-143">En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:</span><span class="sxs-lookup"><span data-stu-id="397c3-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="397c3-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="397c3-144">`Program.Main`:</span></span>

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

<span data-ttu-id="397c3-145">Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:</span><span class="sxs-lookup"><span data-stu-id="397c3-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="397c3-146">Para leer otros archivos de configuración de la carpeta *wwwroot* en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.</span><span class="sxs-lookup"><span data-stu-id="397c3-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="397c3-147">Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="397c3-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="397c3-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="397c3-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="397c3-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="397c3-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="397c3-150">Configuración de autenticación</span><span class="sxs-lookup"><span data-stu-id="397c3-150">Authentication configuration</span></span>

<span data-ttu-id="397c3-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="397c3-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="397c3-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="397c3-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="397c3-153">Configuración del registro</span><span class="sxs-lookup"><span data-stu-id="397c3-153">Logging configuration</span></span>

<span data-ttu-id="397c3-154">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="397c3-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="397c3-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="397c3-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="397c3-156">Configuración del generador de host</span><span class="sxs-lookup"><span data-stu-id="397c3-156">Host builder configuration</span></span>

<span data-ttu-id="397c3-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="397c3-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="397c3-158">Configuración almacenada en caché</span><span class="sxs-lookup"><span data-stu-id="397c3-158">Cached configuration</span></span>

<span data-ttu-id="397c3-159">Los archivos de configuración se almacenan en caché para usarlos sin conexión.</span><span class="sxs-lookup"><span data-stu-id="397c3-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="397c3-160">Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.</span><span class="sxs-lookup"><span data-stu-id="397c3-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="397c3-161">Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="397c3-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="397c3-162">Los usuarios tienen versiones en caché de los archivos que siguen usando.</span><span class="sxs-lookup"><span data-stu-id="397c3-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="397c3-163">Los archivos *service-worker.js* y *service-worker-assets.js* de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.</span><span class="sxs-lookup"><span data-stu-id="397c3-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="397c3-164">Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="397c3-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="397c3-165">Registro</span><span class="sxs-lookup"><span data-stu-id="397c3-165">Logging</span></span>

<span data-ttu-id="397c3-166">Para información sobre la compatibilidad con el registro de Blazor WebAssembly, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="397c3-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="397c3-167">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="397c3-167">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="397c3-168">Reflejo del estado de la conexión en la interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="397c3-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="397c3-169">Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="397c3-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="397c3-170">Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.</span><span class="sxs-lookup"><span data-stu-id="397c3-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="397c3-171">Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="397c3-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="397c3-172">En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="397c3-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="397c3-173">Clase de CSS</span><span class="sxs-lookup"><span data-stu-id="397c3-173">CSS class</span></span>                       | <span data-ttu-id="397c3-174">Indica&hellip;</span><span class="sxs-lookup"><span data-stu-id="397c3-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="397c3-175">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-177">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-177">'Blazor'</span></span>
- <span data-ttu-id="397c3-178">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-178">'Identity'</span></span>
- <span data-ttu-id="397c3-179">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-180">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-180">'Razor'</span></span>
- <span data-ttu-id="397c3-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-182">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-184">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-184">'Blazor'</span></span>
- <span data-ttu-id="397c3-185">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-185">'Identity'</span></span>
- <span data-ttu-id="397c3-186">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-187">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-187">'Razor'</span></span>
- <span data-ttu-id="397c3-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-189">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-191">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-191">'Blazor'</span></span>
- <span data-ttu-id="397c3-192">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-192">'Identity'</span></span>
- <span data-ttu-id="397c3-193">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-194">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-194">'Razor'</span></span>
- <span data-ttu-id="397c3-195">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-196">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-198">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-198">'Blazor'</span></span>
- <span data-ttu-id="397c3-199">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-199">'Identity'</span></span>
- <span data-ttu-id="397c3-200">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-201">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-201">'Razor'</span></span>
- <span data-ttu-id="397c3-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-203">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-205">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-205">'Blazor'</span></span>
- <span data-ttu-id="397c3-206">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-206">'Identity'</span></span>
- <span data-ttu-id="397c3-207">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-208">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-208">'Razor'</span></span>
- <span data-ttu-id="397c3-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-210">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-212">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-212">'Blazor'</span></span>
- <span data-ttu-id="397c3-213">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-213">'Identity'</span></span>
- <span data-ttu-id="397c3-214">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-215">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-215">'Razor'</span></span>
- <span data-ttu-id="397c3-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-217">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-219">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-219">'Blazor'</span></span>
- <span data-ttu-id="397c3-220">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-220">'Identity'</span></span>
- <span data-ttu-id="397c3-221">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-222">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-222">'Razor'</span></span>
- <span data-ttu-id="397c3-223">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-224">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-226">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-226">'Blazor'</span></span>
- <span data-ttu-id="397c3-227">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-227">'Identity'</span></span>
- <span data-ttu-id="397c3-228">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-229">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-229">'Razor'</span></span>
- <span data-ttu-id="397c3-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-231">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-233">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-233">'Blazor'</span></span>
- <span data-ttu-id="397c3-234">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-234">'Identity'</span></span>
- <span data-ttu-id="397c3-235">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-236">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-236">'Razor'</span></span>
- <span data-ttu-id="397c3-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-238">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-240">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-240">'Blazor'</span></span>
- <span data-ttu-id="397c3-241">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-241">'Identity'</span></span>
- <span data-ttu-id="397c3-242">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-243">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-243">'Razor'</span></span>
- <span data-ttu-id="397c3-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-245">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-247">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-247">'Blazor'</span></span>
- <span data-ttu-id="397c3-248">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-248">'Identity'</span></span>
- <span data-ttu-id="397c3-249">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-250">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-250">'Razor'</span></span>
- <span data-ttu-id="397c3-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-252">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-254">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-254">'Blazor'</span></span>
- <span data-ttu-id="397c3-255">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-255">'Identity'</span></span>
- <span data-ttu-id="397c3-256">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-257">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-257">'Razor'</span></span>
- <span data-ttu-id="397c3-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-259">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-261">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-261">'Blazor'</span></span>
- <span data-ttu-id="397c3-262">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-262">'Identity'</span></span>
- <span data-ttu-id="397c3-263">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-264">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-264">'Razor'</span></span>
- <span data-ttu-id="397c3-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-265">'SignalR' uid:</span></span> 

<span data-ttu-id="397c3-266">---------------- | --- title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-268">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-268">'Blazor'</span></span>
- <span data-ttu-id="397c3-269">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-269">'Identity'</span></span>
- <span data-ttu-id="397c3-270">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-271">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-271">'Razor'</span></span>
- <span data-ttu-id="397c3-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-273">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-275">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-275">'Blazor'</span></span>
- <span data-ttu-id="397c3-276">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-276">'Identity'</span></span>
- <span data-ttu-id="397c3-277">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-278">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-278">'Razor'</span></span>
- <span data-ttu-id="397c3-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-280">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-282">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-282">'Blazor'</span></span>
- <span data-ttu-id="397c3-283">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-283">'Identity'</span></span>
- <span data-ttu-id="397c3-284">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-285">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-285">'Razor'</span></span>
- <span data-ttu-id="397c3-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-287">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-289">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-289">'Blazor'</span></span>
- <span data-ttu-id="397c3-290">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-290">'Identity'</span></span>
- <span data-ttu-id="397c3-291">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-292">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-292">'Razor'</span></span>
- <span data-ttu-id="397c3-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-294">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-296">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-296">'Blazor'</span></span>
- <span data-ttu-id="397c3-297">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-297">'Identity'</span></span>
- <span data-ttu-id="397c3-298">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-299">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-299">'Razor'</span></span>
- <span data-ttu-id="397c3-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="397c3-301">title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".</span><span class="sxs-lookup"><span data-stu-id="397c3-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="397c3-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="397c3-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="397c3-303">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="397c3-303">'Blazor'</span></span>
- <span data-ttu-id="397c3-304">"Identity"</span><span class="sxs-lookup"><span data-stu-id="397c3-304">'Identity'</span></span>
- <span data-ttu-id="397c3-305">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="397c3-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="397c3-306">"Razor"</span><span class="sxs-lookup"><span data-stu-id="397c3-306">'Razor'</span></span>
- <span data-ttu-id="397c3-307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="397c3-307">'SignalR' uid:</span></span> 

<span data-ttu-id="397c3-308">--------- | | `components-reconnect-show` | Una conexión perdida.</span><span class="sxs-lookup"><span data-stu-id="397c3-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="397c3-309">El cliente intenta volver a conectarse.</span><span class="sxs-lookup"><span data-stu-id="397c3-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="397c3-310">Se muestra el modal.</span><span class="sxs-lookup"><span data-stu-id="397c3-310">Show the modal.</span></span> <span data-ttu-id="397c3-311">| | `components-reconnect-hide`     | Se restablece una conexión activa con el servidor.</span><span class="sxs-lookup"><span data-stu-id="397c3-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="397c3-312">Se oculta el modal.</span><span class="sxs-lookup"><span data-stu-id="397c3-312">Hide the modal.</span></span> <span data-ttu-id="397c3-313">| | `components-reconnect-failed`   | Error de reconexión, probablemente debido a un error de la red.</span><span class="sxs-lookup"><span data-stu-id="397c3-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="397c3-314">Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="397c3-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="397c3-315">| | `components-reconnect-rejected` | Reconexión rechazada.</span><span class="sxs-lookup"><span data-stu-id="397c3-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="397c3-316">Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</span><span class="sxs-lookup"><span data-stu-id="397c3-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="397c3-317">Para volver a cargar la aplicación, llame a `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="397c3-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="397c3-318">Este estado de conexión se puede producir cuando:</span><span class="sxs-lookup"><span data-stu-id="397c3-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="397c3-319">Se produce un bloqueo en el circuito del lado servidor.</span><span class="sxs-lookup"><span data-stu-id="397c3-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="397c3-320">El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.</span><span class="sxs-lookup"><span data-stu-id="397c3-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="397c3-321">Se eliminan las instancias de los componentes con las que el usuario interactúa.</span><span class="sxs-lookup"><span data-stu-id="397c3-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="397c3-322">El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="397c3-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="397c3-323">Modo de representación</span><span class="sxs-lookup"><span data-stu-id="397c3-323">Render mode</span></span>

<span data-ttu-id="397c3-324">Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él.</span><span class="sxs-lookup"><span data-stu-id="397c3-324">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="397c3-325">Esto se configura en la página *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="397c3-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="397c3-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente:</span><span class="sxs-lookup"><span data-stu-id="397c3-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="397c3-327">Se representa previamente en la página.</span><span class="sxs-lookup"><span data-stu-id="397c3-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="397c3-328">Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="397c3-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="397c3-329">Descripción</span><span class="sxs-lookup"><span data-stu-id="397c3-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="397c3-330">Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="397c3-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="397c3-331">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="397c3-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="397c3-332">Representa un marcador para una aplicación Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="397c3-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="397c3-333">La salida del componente no está incluida.</span><span class="sxs-lookup"><span data-stu-id="397c3-333">Output from the component isn't included.</span></span> <span data-ttu-id="397c3-334">Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.</span><span class="sxs-lookup"><span data-stu-id="397c3-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="397c3-335">Representa el componente en HTML estático.</span><span class="sxs-lookup"><span data-stu-id="397c3-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="397c3-336">No se admite la representación de componentes de servidor desde una página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="397c3-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="397c3-337">Configuración del cliente de SignalR para aplicaciones de servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="397c3-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="397c3-338">En ocasiones tendrá que configurar el cliente de SignalR que usan las aplicaciones de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="397c3-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="397c3-339">Por ejemplo, es posible que quiera configurar el registro en el cliente de SignalR para diagnosticar un problema de conexión.</span><span class="sxs-lookup"><span data-stu-id="397c3-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="397c3-340">Para configurar el cliente de SignalR en el archivo *Pages/_Host.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="397c3-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="397c3-341">Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.</span><span class="sxs-lookup"><span data-stu-id="397c3-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="397c3-342">Llame a `Blazor.start` y pase un objeto de configuración que especifique el generador de SignalR.</span><span class="sxs-lookup"><span data-stu-id="397c3-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="397c3-343">Registro</span><span class="sxs-lookup"><span data-stu-id="397c3-343">Logging</span></span>

<span data-ttu-id="397c3-344">Para más información sobre la compatibilidad de registro de servidor de Blazor, vea <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="397c3-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
