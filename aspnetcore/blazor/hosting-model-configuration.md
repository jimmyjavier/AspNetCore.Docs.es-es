---
title: 'title: "Configuración del modelo de hospedaje de ASP.NET Core Blazor" author: guardrex description: "Aprenda sobre la configuración del modelo de hospedaje de Blazor, incluida la integración de componentes de Razor en aplicaciones Razor Pages y MVC".'
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: e3b8b91a570210e77f307c49f7be21eeab714daa
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355115"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>"Blazor"

"Identity"

"Let's Encrypt"

## <a name="blazor-webassembly"></a>"Razor"

### <a name="environment"></a>"SignalR" uid: blazor/hosting-model-configuration

Configuración del modelo de hospedaje de Blazor en ASP.NET Core Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

En este artículo se describe la configuración del modelo de hospedaje. Blazor WebAssembly Entorno Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo.

Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción. Una aplicación Blazor WebAssembly hospedada selecciona el entorno desde el servidor a través de un middleware que comunica el entorno con el explorador mediante la adición del encabezado `blazor-environment`.

El valor del encabezado es el entorno. La aplicación Blazor hospedada y la aplicación de servidor comparten el mismo entorno.

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
> Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.

En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo.

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo *web.config* publicado.

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

El archivo *web.config* se encuentra en la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish*:

### <a name="configuration"></a>Si quiere usar un archivo *web.config* personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta *publish*, consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Para obtener el entorno de la aplicación en un componente, inserte <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> y lea la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>:

* Durante el inicio, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expone el <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> a través de la propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:
  * Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:
  * La propiedad <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> se puede usar durante el inicio cuando el servicio <xref:Microsoft.AspNetCore.Components.NavigationManager> no está disponible.
* Configuración Blazor WebAssembly carga la configuración desde: Archivos de configuración de aplicaciones de forma predeterminada:

> [!WARNING]
> *wwwroot/appsettings.json* *wwwroot/appsettings.{ENTORNO}.json*

Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación

#### <a name="app-settings-configuration"></a>No todos los proveedores son adecuados para las aplicaciones Blazor WebAssembly.

En [Explicación de los proveedores de configuración de Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134), se explican los proveedores que se admiten en Blazor WebAssembly.

```json
{
  "message": "Hello from config!"
}
```

La configuración de una aplicación Blazor WebAssembly es visible para los demás usuarios.

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>**No almacene credenciales ni secretos de aplicación en la configuración.**

Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.

Configuración de aplicaciones

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

*wwwroot/appsettings.json*:

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

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración: Configuración de proveedor

En el ejemplo siguiente se usa <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> para proporcionar configuración adicional:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

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

#### <a name="authentication-configuration"></a>Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:

Para leer otros archivos de configuración de la carpeta *wwwroot* en la configuración, use <xref:System.Net.Http.HttpClient> para obtener el contenido del archivo.

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Al usar este método, el registro del servicio <xref:System.Net.Http.HttpClient> existente puede usar el cliente local creado para leer el archivo, como se muestra en el ejemplo siguiente:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a>*wwwroot/cars.json*:

`Program.Main`:

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

Configuración de autenticación

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

*wwwroot/appsettings.json*:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>`Program.Main`:

Configuración del registro

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Agregue una referencia de paquete para [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):

*wwwroot/appsettings.json*: `Program.Main`: Configuración del generador de host

* `Program.Main`:
* Configuración almacenada en caché

Los archivos de configuración se almacenan en caché para usarlos sin conexión.

### <a name="logging"></a>Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva.

Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:

## <a name="blazor-server"></a>Los usuarios tienen versiones en caché de los archivos que siguen usando.

### <a name="reflect-the-connection-state-in-the-ui"></a>Los archivos *service-worker.js* y *service-worker-assets.js* de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.

Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>. Registro

Para información sobre la compatibilidad con el registro de Blazor WebAssembly, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Servidor de Blazor

| Reflejo del estado de la conexión en la interfaz de usuario                       | Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse. |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo. Para personalizar la interfaz de usuario, defina un elemento con un valor de `id` de `components-reconnect-modal` en el elemento `<body>` de la página de *_Host.cshtml* Razor: En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`. |
| `components-reconnect-hide`     | Clase de CSS Indica&hellip; |
| `components-reconnect-failed`   | Una conexión perdida. El cliente intenta volver a conectarse. |
| `components-reconnect-rejected` | Se muestra el modal. Se restablece una conexión activa con el servidor. Se oculta el modal. Error de reconexión, probablemente debido a un error de la red.<ul><li>Para intentar la reconexión, llame a `window.Blazor.reconnect()`.</li><li>Reconexión rechazada. Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor.</li><li>Para volver a cargar la aplicación, llame a `location.reload()`.</li></ul> |

### <a name="render-mode"></a>Este estado de conexión se puede producir cuando:

Se produce un bloqueo en el circuito del lado servidor. El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario.

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

Se eliminan las instancias de los componentes con las que el usuario interactúa.

* El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.
* Modo de representación

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | Las aplicaciones Blazor Server se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión del cliente con él. |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Esto se configura en la página *_Host.cshtml* Razor: <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura si el componente: |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Se representa previamente en la página. Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario. Descripción |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server. |

Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Representa un marcador para una aplicación Blazor Server.

La salida del componente no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor.

Representa el componente en HTML estático.

* No se admite la representación de componentes de servidor desde una página HTML estática.
* Configuración del cliente de SignalR para aplicaciones de servidor Blazor

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

### <a name="logging"></a>En ocasiones tendrá que configurar el cliente de SignalR que usan las aplicaciones de servidor Blazor.

Por ejemplo, es posible que quiera configurar el registro en el cliente de SignalR para diagnosticar un problema de conexión.
