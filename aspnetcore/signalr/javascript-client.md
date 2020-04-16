---
title: ASP.NET SignalR cliente JavaScript principal
author: bradygaster
description: Descripción general SignalR de ASP.NET cliente JavaScript principal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 43b2cacf9f415ec422a00b28246f30c8ad74de29
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440862"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR cliente JavaScript principal

Por [Rachel Appel](https://twitter.com/rachelappel)

La biblioteca SignalR de cliente JavaScript de ASP.NET Core permite a los desarrolladores llamar a código de concentrador del lado del servidor.

[Ver o descargar código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ( cómo[descargar](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Instale SignalR el paquete de cliente

La SignalR biblioteca de cliente de JavaScript se entrega como un paquete [npm.](https://www.npmjs.com/) En las secciones siguientes se describen diferentes formas de instalar la biblioteca cliente.

### <a name="install-with-npm"></a>Instalar con npm

Si usa Visual Studio, ejecute los siguientes comandos desde la consola del Administrador de **paquetes** mientras se encuentra en la carpeta raíz. Para Visual Studio Code, ejecute los siguientes comandos desde el **terminal integrado**.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm instala el contenido del paquete en la carpeta *node_modules.\\ * Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot\\lib.* Copie el archivo *signalr.js* en la carpeta *wwwroot-lib-signalr.*

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm instala el contenido del paquete en la carpeta *node_modules.\\ * Cree una nueva carpeta denominada *signalr* en la carpeta *wwwroot\\lib.* Copie el archivo *signalr.js* en la carpeta *wwwroot-lib-signalr.*

::: moniker-end

Haga SignalR referencia al cliente `<script>` JavaScript en el elemento. Por ejemplo:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Usar una red de entrega de contenido (CDN)

Para utilizar la biblioteca de cliente sin el requisito previo npm, haga referencia a una copia hospedada en CDN de la biblioteca de cliente. Por ejemplo:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

La biblioteca cliente está disponible en las siguientes CDN:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>Instalar con LibMan

[LibMan](xref:client-side/libman/index) se puede utilizar para instalar archivos de biblioteca de cliente específicos desde la biblioteca de cliente hospedada en CDN. Por ejemplo, solo agregue el archivo JavaScript minimizado al proyecto. Para obtener más información sobre este enfoque, consulte [Agregar la SignalR biblioteca de cliente.](xref:tutorials/signalr#add-the-signalr-client-library)

## <a name="connect-to-a-hub"></a>Conéctese a un concentrador

El código siguiente crea e inicia una conexión. El nombre del concentrador no distingue mayúsculas de minúsculas.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Conexiones entre orígenes

Normalmente, los exploradores cargan conexiones desde el mismo dominio que la página solicitada. Sin embargo, hay ocasiones en las que se requiere una conexión a otro dominio.

Para evitar que un sitio malintencionado lea datos confidenciales de otro sitio, [las conexiones entre orígenes](xref:security/cors) están deshabilitadas de forma predeterminada. Para permitir una solicitud entre orígenes, habilitela en la `Startup` clase.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Métodos de concentrador de llamadas desde el cliente

Los clientes JavaScript llaman a métodos públicos en concentradores a través del método [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). El `invoke` método acepta dos argumentos:

* El nombre del método hub. En el ejemplo siguiente, el nombre `SendMessage`del método en el concentrador es .
* Cualquier argumento definido en el método de concentrador. En el ejemplo siguiente, `message`el nombre del argumento es . El código de ejemplo utiliza la sintaxis de función de flecha que se admite en las versiones actuales de todos los exploradores principales, excepto Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Si usa Azure SignalR Service en modo *sin servidor,* no puede llamar a métodos de concentrador desde un cliente. Para obtener más información, consulte la [ SignalR documentación del servicio](/azure/azure-signalr/signalr-concept-serverless-development-config).

El `invoke` método devuelve un JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). Se `Promise` resuelve con el valor devuelto (si existe) cuando devuelve el método en el servidor. Si el método en el servidor `Promise` produce un error, se rechaza con el mensaje de error. Utilice `then` los `catch` métodos `Promise` y en sí `await` mismo para controlar estos casos (o sintaxis).

El `send` método devuelve `Promise`un archivo JavaScript . El `Promise` se resuelve cuando el mensaje se ha enviado al servidor. Si hay un error al `Promise` enviar el mensaje, se rechaza con el mensaje de error. Utilice `then` los `catch` métodos `Promise` y en sí `await` mismo para controlar estos casos (o sintaxis).

> [!NOTE]
> El `send` uso no espera hasta que el servidor haya recibido el mensaje. Por lo tanto, no es posible devolver datos o errores del servidor.

## <a name="call-client-methods-from-hub"></a>Llamar a métodos de cliente desde el concentrador

Para recibir mensajes del concentrador, defina [on](/javascript/api/%40aspnet/signalr/hubconnection#on) un método `HubConnection`mediante el método on del archivo .

* El nombre del método de cliente JavaScript. En el ejemplo siguiente, `ReceiveMessage`el nombre del método es .
* Argumentos que el concentrador pasa al método. En el ejemplo siguiente, `message`el valor del argumento es .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

El código anterior `connection.on` en se ejecuta cuando el código del lado servidor lo llama mediante el [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) método.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRdetermina qué método de cliente llamar haciendo coincidir el `SendAsync` `connection.on`nombre del método y los argumentos definidos en y .

> [!NOTE]
> Como práctica recomendada, llame al `HubConnection` método `on` [start](/javascript/api/%40aspnet/signalr/hubconnection#start) en el archivo . Al hacerlo, se garantiza que los controladores se registren antes de recibir los mensajes.

## <a name="error-handling-and-logging"></a>Registro y control de errores

Encadenar un `catch` método `start` al final del método para controlar los errores del lado cliente. Se `console.error` utiliza para generar errores en la consola del navegador.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Configure el seguimiento de registros del lado cliente pasando un registrador y el tipo de evento para registrar cuando se realiza la conexión. Los mensajes se registran con el nivel de registro especificado y superior. Los niveles de registro disponibles son los siguientes:

* `signalR.LogLevel.Error`&ndash; Mensajes de error. Solo `Error` registra mensajes.
* `signalR.LogLevel.Warning`&ndash; Mensajes de advertencia sobre posibles errores. Registros `Warning` `Error` y mensajes.
* `signalR.LogLevel.Information`&ndash; Mensajes de estado sin errores. `Information`Registros `Warning`, `Error` , y mensajes.
* `signalR.LogLevel.Trace`&ndash; Mensajes de seguimiento. Registra todo, incluidos los datos transportados entre el concentrador y el cliente.

Utilice el método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar el nivel de registro. Los mensajes se registran en la consola del explorador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Reconectar clientes

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconexión automática

El cliente JavaScript para SignalR se puede configurar `withAutomaticReconnect` para volver a conectarse automáticamente mediante el método en [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). No se volverá a conectar automáticamente de forma predeterminada.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Sin ningún `withAutomaticReconnect()` parámetro, configura al cliente para esperar 0, 2, 10, y 30 segundos respectivamente antes de intentar cada intento de reconexión, deteniendo después de cuatro intentos fallidos.

Antes de iniciar cualquier `HubConnection` intento de `HubConnectionState.Reconnecting` reconexión, `onreconnecting` el will pasará al `Disconnected` estado y disparar `onclose` sus devoluciones de llamada en lugar de pasar al estado y desencadenar sus devoluciones de llamada como un `HubConnection` sin reconexión automática configurada. Esto proporciona una oportunidad para advertir a los usuarios de que se ha perdido la conexión y deshabilitar los elementos de la interfaz de usuario.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Si el cliente se vuelve a conectar `HubConnection` correctamente dentro de `Connected` sus primeros `onreconnected` cuatro intentos, volverá a pasar al estado y desencadenará sus devoluciones de llamada. Esto proporciona una oportunidad para informar a los usuarios de que se ha restablecido la conexión.

Puesto que la conexión parece totalmente `connectionId` nueva en el `onreconnected` servidor, se proporcionará un nuevo a la devolución de llamada.

> [!WARNING]
> El `onreconnected` parámetro `connectionId` de la devolución de `HubConnection` llamada no estará definido si se configuró para omitir la [negociación.](xref:signalr/configuration#configure-client-options)

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`no configurará `HubConnection` el para reintentar los errores de inicio iniciales, por lo que los errores de inicio deben controlarse manualmente:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Si el cliente no se vuelve a conectar correctamente `HubConnection` dentro de `Disconnected` sus primeros cuatro intentos, el pasará al estado y desencadenará sus devoluciones de llamada [onclose.](/javascript/api/%40aspnet/signalr/hubconnection#onclose) Esto proporciona una oportunidad para informar a los usuarios de que la conexión se ha perdido permanentemente y recomienda actualizar la página:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Para configurar un número personalizado de intentos de reconexión antes `withAutomaticReconnect` de desconectar o cambiar la sincronización de reconexión, valida una matriz de números que representan el retardo en milisegundos para esperar antes de comenzar cada intento de reconexión.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

En el ejemplo anterior `HubConnection` se configura el para comenzar a intentar volver a conectarse inmediatamente después de que se pierda la conexión. Esto también es cierto para la configuración predeterminada.

Si el primer intento de reconexión falla, el segundo intento de reconexión también se iniciará inmediatamente en lugar de esperar 2 segundos como lo haría en la configuración predeterminada.

Si el segundo intento de reconexión falla, el tercer intento de reconexión comenzará en 10 segundos que es otra vez como la configuración predeterminada.

A continuación, el comportamiento personalizado vuelve a divergir del comportamiento predeterminado deteniéndose después del tercer error del intento de reconexión en lugar de intentar un intento de reconexión más en otros 30 segundos como lo haría en la configuración predeterminada.

Si desea un mayor control sobre la sincronización `withAutomaticReconnect` y el número `IRetryPolicy` de intentos de reconexión automática, acepta un objeto que implementa la interfaz, que tiene un único método denominado `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds`toma un único argumento `RetryContext`con el tipo . Tiene `RetryContext` tres `previousRetryCount`propiedades: `elapsedMilliseconds` `retryReason` , y `number`que `number` son `Error` un , a y un respectivamente. Antes del primer intento `previousRetryCount` de `elapsedMilliseconds` reconexión, ambos `retryReason` y serán cero, y el será el error que causó la conexión que se perdió. Después de cada `previousRetryCount` intento fallido de reintento, se incrementará en uno, `elapsedMilliseconds` se actualizará para reflejar `retryReason` la cantidad de tiempo invertido en volver a conectarse hasta ahora en milisegundos, y el será el error que causó el último intento de reconexión fallar.

`nextRetryDelayInMilliseconds`debe devolver un número que represente el número de milisegundos para esperar antes del siguiente intento de reconexión o `null` si debe `HubConnection` dejar de conectarse.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Como alternativa, puede escribir código que vuelva a conectar el cliente manualmente como se muestra en [Volver a conectar manualmente.](#manually-reconnect)

::: moniker-end

### <a name="manually-reconnect"></a>Reconexión manual

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes de 3.0, el SignalR cliente JavaScript para no se vuelve a conectar automáticamente. Debe escribir código que vuelva a conectar el cliente manualmente.

::: moniker-end

El código siguiente muestra un enfoque típico de reconexión manual:

1. Se crea una función `start` (en este caso, la función) para iniciar la conexión.
1. Llame `start` a la función `onclose` en el controlador de eventos de la conexión.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Una implementación del mundo real usaría un retroceso exponencial o volvería a intentarlo un número especificado de veces antes de darse por vencido.

## <a name="additional-resources"></a>Recursos adicionales

* [Referencia de API de JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutorial de JavaScript](xref:tutorials/signalr)
* [Tutorial webPack y TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Centros](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Publicar en Azure](xref:signalr/publish-to-azure-web-app)
* [Solicitudes entre orígenes (CORS)](xref:security/cors)
* [Documentación SignalR sin servidor de Azure Service](/azure/azure-signalr/signalr-concept-serverless-development-config)
