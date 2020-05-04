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
# <a name="aspnet-core-blazor-hosting-model-configuration"></a>Configuración del modelo de hospedaje de Blazor en ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) y [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

En este artículo se describe la configuración del modelo de hospedaje.

## <a name="blazor-webassembly"></a>WebAssembly de Blazor

### <a name="environment"></a>Entorno

Cuando una aplicación se ejecuta de manera local, el entorno se establece de manera predeterminada en Desarrollo. Cuando la aplicación se publica, el entorno se establece de manera predeterminada en Producción.

Una aplicación WebAssembly de Blazor hospedada selecciona el entorno desde el servidor a través de un middleware agrega el encabezado `blazor-environment` para comunicar el entorno con el explorador. El valor del encabezado es el entorno. La aplicación de Blazor hospedada y la aplicación de servidor comparten el mismo entorno. Para más información, incluida la manera de configurar el entorno, consulte <xref:fundamentals/environments>.

En el caso de una aplicación independiente que se ejecuta localmente, el servidor de desarrollo agrega el encabezado `blazor-environment` para especificar el entorno de Desarrollo. Para especificar el entorno para otros entornos de hospedaje, agregue el encabezado `blazor-environment`.

En el ejemplo siguiente de IIS, agregue el encabezado personalizado al archivo *web.config* publicado. El archivo *web.config* se encuentra en la carpeta *bin/Release/{PLATAFORMA DE DESTINO}/publish*:

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
> Si quiere usar un archivo *web.config* personalizado para IIS que no se sobrescriba cuando la aplicación se publique en la carpeta *publish*, consulte <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.

Para obtener el entorno de la aplicación en un componente, inserte `IWebAssemblyHostEnvironment` y lea la propiedad `Environment`:

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

Durante el inicio, `WebAssemblyHostBuilder` expone el `IWebAssemblyHostEnvironment` a través de la propiedad `HostEnvironment`, lo que permite que los desarrolladores tengan una lógica específica del entorno en el código:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Los métodos de extensión útiles siguientes permiten comprobar el entorno actual para Desarrollo, Producción, Ensayo y nombres de entornos personalizados:

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{NOMBRE DE ENTORNO}")

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

La propiedad `IWebAssemblyHostEnvironment.BaseAddress` se puede usar durante el inicio cuando el servicio `NavigationManager` no está disponible.

### <a name="configuration"></a>Configuración

WebAssembly de Blazor admite configuraciones procedentes de:

* El [proveedor de configuración de archivos](xref:fundamentals/configuration/index#file-configuration-provider) de archivos de configuración de la aplicación de forma predeterminada:
  * *wwwroot/appsettings.json*
  * *wwwroot/appsettings.{ENTORNO}.json*
* Otros [proveedores de configuración](xref:fundamentals/configuration/index) registrados por la aplicación

> [!WARNING]
> La configuración de una aplicación de Blazor WebAssembly es visible para los demás usuarios. **No almacene credenciales ni secretos de aplicación en la configuración.**

Para más información sobre los proveedores de configuración, vea <xref:fundamentals/configuration/index>.

#### <a name="app-settings-configuration"></a>Configuración de aplicaciones

*wwwroot/appsettings.json*:

```json
{
  "message": "Hello from config!"
}
```

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a>Configuración de proveedor

En el siguiente ejemplo se usa un elemento <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> y el [proveedor de configuración de archivos](xref:fundamentals/configuration/index#file-configuration-provider) para proporcionar una configuración adicional:

`Program.Main`:

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

Inserte una instancia <xref:Microsoft.Extensions.Configuration.IConfiguration> en un componente para acceder a los datos de configuración:

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

#### <a name="authentication-configuration"></a>Configuración de autenticación

*wwwroot/appsettings.json*:

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a>Configuración del registro

*wwwroot/appsettings.json*:

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

`Program.Main`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a>Configuración del generador de host

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a>Configuración almacenada en caché

Los archivos de configuración se almacenan en caché para usarlos sin conexión. Con [Aplicaciones web progresivas (PWA)](xref:blazor/progressive-web-app), solo puede actualizar los archivos de configuración al crear una implementación nueva. Editar los archivos de configuración entre las implementaciones no tiene ningún efecto, debido a lo siguiente:

* Los usuarios tienen versiones en caché de los archivos que siguen usando.
* Los archivos *service-worker.js* y *service-worker-assets.js* de PWA se deben recompilar en la compilación, lo que indica a la aplicación en la próxima visita en línea del usuario que la aplicación se volvió a implementar.

Para más información sobre cómo PWA administra las actualizaciones en segundo plano, consulte <xref:blazor/progressive-web-app#background-updates>.

### <a name="logging"></a>Registro

Para información sobre la compatibilidad con el registro de WebAssembly de Blazor, consulte <xref:fundamentals/logging/index#create-logs-in-blazor>.

## <a name="blazor-server"></a>Servidor Blazor

### <a name="reflect-the-connection-state-in-the-ui"></a>Reflejo del estado de la conexión en la interfaz de usuario

Cuando el cliente detecta que se ha perdido la conexión, se muestra al usuario una interfaz de usuario predeterminada mientras el cliente intenta volver a conectarse. Si se produce un error en la reconexión, se proporciona al usuario la opción de volver a intentarlo.

Para personalizar la interfaz de usuario, defina un elemento con un valor `id` de `components-reconnect-modal` en el elemento `<body>` de la página de Razor *_Host.cshtml*:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

En la tabla siguiente se describen las clases de CSS aplicadas al elemento `components-reconnect-modal`.

| Clase de CSS                       | Indica&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Una conexión perdida. El cliente intenta volver a conectarse. Se muestra el modal. |
| `components-reconnect-hide`     | Se restablece una conexión activa con el servidor. Se oculta el modal. |
| `components-reconnect-failed`   | Error de reconexión, probablemente debido a un error de la red. Para intentar la reconexión, llame a `window.Blazor.reconnect()`. |
| `components-reconnect-rejected` | Reconexión rechazada. Se ha alcanzado el servidor pero se ha rechazado la conexión y se ha perdido el estado del usuario en el servidor. Para volver a cargar la aplicación, llame a `location.reload()`. Este estado de conexión se puede producir cuando:<ul><li>Se produce un bloqueo en el circuito del lado servidor.</li><li>El cliente se desconecta el tiempo suficiente para que el servidor quite el estado del usuario. Se eliminan las instancias de los componentes con las que el usuario interactúa.</li><li>El servidor se reinicia o se recicla el proceso de trabajo de la aplicación.</li></ul> |

### <a name="render-mode"></a>Modo de representación

Las aplicaciones de servidor Blazor se configuran de forma predeterminada para realizar una representación previa de la interfaz de usuario en el servidor antes de que se establezca la conexión de cliente con el servidor. Esto se configura en la página de Razor *_Host.cshtml*:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode` configura si el componente:

* Se representa previamente en la página.
* Se representa como HTML estático en la página o si incluye la información necesaria para arrancar una aplicación Blazor desde el agente de usuario.

| `RenderMode`        | Descripción |
| ------------------- | ----------- |
| `ServerPrerendered` | Representa el componente en código HTML estático e incluye un marcador para una aplicación Blazor Server. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| `Server`            | Representa un marcador para una aplicación Blazor Server. La salida del componente no está incluida. Cuando se inicia el agente de usuario, este marcador se usa para arrancar una aplicación Blazor. |
| `Static`            | Representa el componente en HTML estático. |

No se admite la representación de componentes de servidor desde una página HTML estática.

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a>Configuración del cliente de SignalR para aplicaciones de servidor Blazor

En ocasiones tendrá que configurar el cliente de SignalR que usan las aplicaciones de servidor Blazor. Por ejemplo, es posible que quiera configurar el registro en el cliente de SignalR para diagnosticar un problema de conexión.

Para configurar el cliente de SignalR en el archivo *Pages/_Host.cshtml*:

* Agregue un atributo `autostart="false"` a la etiqueta `<script>` para el script `blazor.server.js`.
* Llame a `Blazor.start` y pase un objeto de configuración que especifique el generador de SignalR.

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

### <a name="logging"></a>Registro

Para más información sobre la compatibilidad de registro de servidor de Blazor, vea <xref:fundamentals/logging/index#create-logs-in-blazor>.
