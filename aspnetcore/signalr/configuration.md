---
title: Configuración SignalR de ASP.NET Core
author: bradygaster
description: Obtén información sobre SignalR cómo configurar aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228145"
---
# <a name="aspnet-core-signalr-configuration"></a>Configuración de ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`se puede agregar después `Startup.ConfigureServices`de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en . El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto. La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ese objeto es un objeto que se puede usar para configurar la serialización de argumentos y valores devueltos. Para obtener más información, consulte la [documentación de System.Text.Json](/dotnet/api/system.text.json).

Por ejemplo, para configurar el serializador para que no cambie la mayúsculas y minúsculas de `Startup.ConfigureServices`los nombres de propiedad, en lugar de los nombres predeterminados de "camelCase", utilice el código siguiente en:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> No es posible configurar la serialización JSON en el cliente JavaScript en este momento.

### <a name="switch-to-newtonsoftjson"></a>Cambiar a Newtonsoft.Json

Si necesita características `Newtonsoft.Json` que no son `System.Text.Json`compatibles en , consulte [Cambiar a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada. Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.

## <a name="configure-server-options"></a>Configurar las opciones del servidor

En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo. Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto. El valor recomendado `KeepAliveInterval` es el doble del valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta. Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este concentrador. De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales. |
| `EnableDetailedErrors` | `false` | Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub. El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial. |
| `StreamBufferCapacity` | `10` | El número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente. Si se alcanza este límite, el procesamiento de invocaciones se bloquea hasta que el servidor procesa los elementos de secuencia.|
| `MaximumReceiveMessageSize` | 32 KB | Tamaño máximo de un único mensaje de concentrador entrante. |

Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones avanzadas de configuración HTTP

Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | El número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la contrapresión. Aumentar este valor permite al servidor recibir mensajes más grandes más rápidamente sin aplicar contrapresión, pero puede aumentar el consumo de memoria. |
| `AuthorizationData` | Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub. | Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador. |
| `TransportMaxBufferSize` | 32 KB | El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión. Aumentar este valor permite al servidor almacenar en búfer mensajes más grandes más rápidamente sin esperar la contrapresión, pero puede aumentar el consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse. |
| `LongPolling` | Véase a continuación. | Opciones adicionales específicas para el transporte de sondeo largo. |
| `WebSockets` | Véase a continuación. | Opciones adicionales específicas del transporte de WebSockets. |
| `MinimumProtocolVersion` | 0 | Especifique la versión mínima del protocolo de negociación. Esto se utiliza para limitar los clientes a las versiones más recientes. |

El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo. La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia. |

El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configurar las opciones de cliente

Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript). También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.

### <a name="configure-logging"></a>registro

El registro se configura en `ConfigureLogging` el cliente .NET mediante el método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet. Llame `AddConsole` al método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente JavaScript, existe un método similar. `configureLogging` Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

En lugar `LogLevel` de un valor, `string` también puede proporcionar un valor que represente un nombre de nivel de registro. Esto es útil al configurar el registro de SignalR en `LogLevel` entornos donde no tiene acceso a las constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

En la tabla siguiente se enumeran los niveles de registro disponibles. El valor que `configureLogging` proporcione para establecer el nivel de registro **mínimo** que se registrará. Los mensajes registrados en este nivel, **o los niveles enumerados después de él en la tabla,** se registrarán.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o**`information` | `LogLevel.Information` |
| `warn`**o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.

Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).

El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede ignorar de forma segura.

### <a name="configure-allowed-transports"></a>Configurar los transportes permitidos

Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript). Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión de los websockets del cliente Java es el único transporte disponible.

En el cliente Java, el `withTransport` transporte se `HttpHubConnectionBuilder`selecciona con el método en el archivo . El cliente Java utiliza de forma predeterminada el transporte webSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> El cliente Java de SignalR todavía no admite la reserva de transporte.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación del portador

Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ). En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets). En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.

En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar el tiempo de espera y mantener las opciones de mantener en vivo

Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript). Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript). Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `ClientCertificates` | Vacío | Colección de certificados TLS para enviar para autenticar solicitudes. |
| `Cookies` | Vacío | Una colección de cookies HTTP para enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales para enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Sólo WebSockets. La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP. No se utiliza para conexiones WebSocket. Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Un proxy HTTP para usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto permite el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones de WebSocket adicionales. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `skipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opción Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `withHeader` `withHeaders` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |

---

En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) `AddJsonProtocol`se puede agregar después `Startup.ConfigureServices`de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en . El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto. La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ese objeto es un objeto que se puede usar para configurar la serialización de argumentos y valores devueltos. Para obtener más información, consulte la [documentación de System.Text.Json](/dotnet/api/system.text.json).

Por ejemplo, para configurar el serializador para que no cambie la mayúsculas y minúsculas de `Startup.ConfigureServices`los nombres de propiedad, en lugar de los nombres predeterminados de "camelCase", utilice el código siguiente en:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> No es posible configurar la serialización JSON en el cliente JavaScript en este momento.

### <a name="switch-to-newtonsoftjson"></a>Cambiar a Newtonsoft.Json

Si necesita características `Newtonsoft.Json` que no son `System.Text.Json`compatibles en , consulte [Cambiar a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada. Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.

## <a name="configure-server-options"></a>Configurar las opciones del servidor

En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo. Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto. El valor recomendado `KeepAliveInterval` es el doble del valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta. Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este concentrador. De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales. |
| `EnableDetailedErrors` | `false` | Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub. El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial. |
| `StreamBufferCapacity` | `10` | El número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente. Si se alcanza este límite, el procesamiento de invocaciones se bloquea hasta que el servidor procesa los elementos de secuencia.|
| `MaximumReceiveMessageSize` | 32 KB | Tamaño máximo de un único mensaje de concentrador entrante. |

Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones avanzadas de configuración HTTP

Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | El número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la contrapresión. Aumentar este valor permite al servidor recibir mensajes más grandes más rápidamente sin aplicar contrapresión, pero puede aumentar el consumo de memoria. |
| `AuthorizationData` | Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub. | Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador. |
| `TransportMaxBufferSize` | 32 KB | El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión. Aumentar este valor permite al servidor almacenar en búfer mensajes más grandes más rápidamente sin esperar la contrapresión, pero puede aumentar el consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse. |
| `LongPolling` | Véase a continuación. | Opciones adicionales específicas para el transporte de sondeo largo. |
| `WebSockets` | Véase a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo. La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia. |

El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configurar las opciones de cliente

Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript). También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.

### <a name="configure-logging"></a>registro

El registro se configura en `ConfigureLogging` el cliente .NET mediante el método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet. Llame `AddConsole` al método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente JavaScript, existe un método similar. `configureLogging` Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

En lugar `LogLevel` de un valor, `string` también puede proporcionar un valor que represente un nombre de nivel de registro. Esto es útil al configurar el registro de SignalR en `LogLevel` entornos donde no tiene acceso a las constantes.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

En la tabla siguiente se enumeran los niveles de registro disponibles. El valor que `configureLogging` proporcione para establecer el nivel de registro **mínimo** que se registrará. Los mensajes registrados en este nivel, **o los niveles enumerados después de él en la tabla,** se registrarán.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o**`information` | `LogLevel.Information` |
| `warn`**o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.

Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).

El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede ignorar de forma segura.

### <a name="configure-allowed-transports"></a>Configurar los transportes permitidos

Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript). Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión de los websockets del cliente Java es el único transporte disponible.

En el cliente Java, el `withTransport` transporte se `HttpHubConnectionBuilder`selecciona con el método en el archivo . El cliente Java utiliza de forma predeterminada el transporte webSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> El cliente Java de SignalR todavía no admite la reserva de transporte.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación del portador

Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ). En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets). En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.

En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar el tiempo de espera y mantener las opciones de mantener en vivo

Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript). Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript). Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `ClientCertificates` | Vacío | Colección de certificados TLS para enviar para autenticar solicitudes. |
| `Cookies` | Vacío | Una colección de cookies HTTP para enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales para enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Sólo WebSockets. La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP. No se utiliza para conexiones WebSocket. Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Un proxy HTTP para usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto permite el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones de WebSocket adicionales. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `skipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opción Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `withHeader` `withHeaders` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |

---

En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización JSON se puede configurar en el servidor mediante el método de `Startup.ConfigureServices` extensión [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método. El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto. La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese `JsonSerializerSettings` objeto es un objeto JSON.NET que se puede usar para configurar la serialización de argumentos y valores devueltos. Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Por ejemplo, para configurar el serializador para que utilice los nombres de propiedad "PascalCase", `Startup.ConfigureServices`en lugar de los nombres predeterminados "camelCase", utilice el siguiente código en:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> No es posible configurar la serialización JSON en el cliente JavaScript en este momento.

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada. Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.

## <a name="configure-server-options"></a>Configurar las opciones del servidor

En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 segundos | El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo. Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto. El valor recomendado `KeepAliveInterval` es el doble del valor.|
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta. Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este concentrador. De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales. |
| `EnableDetailedErrors` | `false` | Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub. El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial. |

Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones avanzadas de configuración HTTP

Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | El número máximo de bytes recibidos del cliente que el servidor almacena en búfer. Aumentar este valor permite al servidor recibir mensajes más grandes, pero puede afectar negativamente al consumo de memoria. |
| `AuthorizationData` | Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub. | Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador. |
| `TransportMaxBufferSize` | 32 KB | El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer. Aumentar este valor permite al servidor enviar mensajes más grandes, pero puede afectar negativamente al consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse. |
| `LongPolling` | Véase a continuación. | Opciones adicionales específicas para el transporte de sondeo largo. |
| `WebSockets` | Véase a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo. La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia. |

El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configurar las opciones de cliente

Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript). También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.

### <a name="configure-logging"></a>registro

El registro se configura en `ConfigureLogging` el cliente .NET mediante el método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet. Llame `AddConsole` al método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente JavaScript, existe un método similar. `configureLogging` Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.

Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).

El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede ignorar de forma segura.

### <a name="configure-allowed-transports"></a>Configurar los transportes permitidos

Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript). Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

En esta versión de los websockets del cliente Java es el único transporte disponible.

### <a name="configure-bearer-authentication"></a>Configurar la autenticación del portador

Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ). En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets). En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.

En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar el tiempo de espera y mantener las opciones de mantener en vivo

Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript). Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript). Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `keepAliveIntervalInMilliseconds` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 segundos (15.000 milisegundos) | Determina el intervalo en el que el cliente envía mensajes ping. El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo. Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado. |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `ClientCertificates` | Vacío | Colección de certificados TLS para enviar para autenticar solicitudes. |
| `Cookies` | Vacío | Una colección de cookies HTTP para enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales para enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Sólo WebSockets. La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP. No se utiliza para conexiones WebSocket. Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Un proxy HTTP para usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto permite el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones de WebSocket adicionales. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `skipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opción Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `withHeader` `withHeaders` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |

---

En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opciones de serialización JSON/MessagePack

ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html). Cada protocolo tiene opciones de configuración de serialización.

La serialización JSON se puede configurar en el servidor mediante el método de `Startup.ConfigureServices` extensión [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método. El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto. La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese `JsonSerializerSettings` objeto es un objeto JSON.NET que se puede usar para configurar la serialización de argumentos y valores devueltos. Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Por ejemplo, para configurar el serializador para que utilice los nombres de propiedad "PascalCase", `Startup.ConfigureServices`en lugar de los nombres predeterminados "camelCase", utilice el siguiente código en:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> No es posible configurar la serialización JSON en el cliente JavaScript en este momento.

### <a name="messagepack-serialization-options"></a>Opciones de serialización de MessagePack

MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada. Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.

> [!NOTE]
> No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.

## <a name="configure-server-options"></a>Configurar las opciones del servidor

En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 segundos | Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 segundos | Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta. Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente. El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.  |
| `SupportedProtocols` | Todos los protocolos instalados | Protocolos admitidos por este concentrador. De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales. |
| `EnableDetailedErrors` | `false` | Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub. El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial. |

Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opciones avanzadas de configuración HTTP

Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria. Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | El número máximo de bytes recibidos del cliente que el servidor almacena en búfer. Aumentar este valor permite al servidor recibir mensajes más grandes, pero puede afectar negativamente al consumo de memoria. |
| `AuthorizationData` | Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub. | Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador. |
| `TransportMaxBufferSize` | 32 KB | El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer. Aumentar este valor permite al servidor enviar mensajes más grandes, pero puede afectar negativamente al consumo de memoria. |
| `Transports` | Todos los transportes están habilitados. | Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse. |
| `LongPolling` | Véase a continuación. | Opciones adicionales específicas para el transporte de sondeo largo. |
| `WebSockets` | Véase a continuación. | Opciones adicionales específicas del transporte de WebSockets. |

El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 segundos | La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo. La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia. |

El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 segundos | Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión. |
| `SubProtocolSelector` | `null` | Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado. El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado. |

## <a name="configure-client-options"></a>Configurar las opciones de cliente

Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript). También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.

### <a name="configure-logging"></a>registro

El registro se configura en `ConfigureLogging` el cliente .NET mediante el método. Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor. Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.

> [!NOTE]
> Para registrar los proveedores de registro, debe instalar los paquetes necesarios. Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.

Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet. Llame `AddConsole` al método de extensión:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

En el cliente JavaScript, existe un método similar. `configureLogging` Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán. Los registros se escriben en la ventana de la consola del explorador.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.

Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).

El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro. Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica. El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Esto se puede ignorar de forma segura.

### <a name="configure-allowed-transports"></a>Configurar los transportes permitidos

Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript). Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados. Todos los transportes están habilitados de forma predeterminada.

Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurar la autenticación del portador

Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado. En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ). En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets). En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.

En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurar el tiempo de espera y mantener las opciones de mantener en vivo

Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript). Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |
| `HandshakeTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript). Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings. |

# <a name="java"></a>[Java](#tab/java)

| Opción | Valor predeterminado | Descripción |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 segundos (30.000 milisegundos) | Tiempo de espera para la actividad del servidor. Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento. Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera. El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor, para permitir el tiempo para que lleguen los pings. |
| `withHandshakeResponseTimeout` | 15 segundos | Tiempo de espera para el apretón de manos inicial del servidor. Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento. Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave. Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configuración de opciones adicionales

Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opción .NET |  Valor predeterminado | Descripción |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `SkipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `ClientCertificates` | Vacío | Colección de certificados TLS para enviar para autenticar solicitudes. |
| `Cookies` | Vacío | Una colección de cookies HTTP para enviar con cada solicitud HTTP. |
| `Credentials` | Vacío | Credenciales para enviar con cada solicitud HTTP. |
| `CloseTimeout` | 5 segundos | Sólo WebSockets. La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre. Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta. |
| `Headers` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |
| `HttpMessageHandlerFactory` | `null` | Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP. No se utiliza para conexiones WebSocket. Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro. Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia. **Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.** |
| `Proxy` | `null` | Un proxy HTTP para usar al enviar solicitudes HTTP. |
| `UseDefaultCredentials` | `false` | Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets. Esto permite el uso de la autenticación de Windows. |
| `WebSocketConfiguration` | `null` | Delegado que se puede usar para configurar opciones de WebSocket adicionales. Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opción JavaScript | Valor predeterminado | Descripción |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `skipNegotiation` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opción Java | Valor predeterminado | Descripción |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP. |
| `shouldSkipNegotiate` | `false` | Establezca esta `true` opción para omitir el paso de negociación. **Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.** Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR. |
| `withHeader` `withHeaders` | Vacío | Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP. |

---

En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
