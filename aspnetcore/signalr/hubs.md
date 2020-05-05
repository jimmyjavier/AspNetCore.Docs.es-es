---
title: Usar hubs en ASP.NET CoreSignalR
author: bradygaster
description: Obtenga información sobre cómo usar hubs en SignalRASP.net Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubs
ms.openlocfilehash: 6ea8a8e9ffb6549a285f320eb0a4a2e5d218483a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775224"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Uso de concentradores de SignalR para ASP.NET Core

Por [Rachel Appel](https://twitter.com/rachelappel) y [Kevin Griffin](https://twitter.com/1kevgriff)

[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>¿Qué es un centro Signalr?

Signalr hubs API le permite llamar a métodos en clientes conectados desde el servidor. En el código del servidor, se definen los métodos a los que llama el cliente. En el código de cliente, se definen los métodos a los que se llama desde el servidor. Signalr se encarga de todo en segundo plano que permite la comunicación de cliente a servidor y de servidor a cliente en tiempo real.

## <a name="configure-signalr-hubs"></a>Configurar los concentradores de Signalr

El middleware Signalr requiere algunos servicios, que se configuran `services.AddSignalR`mediante una llamada a.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Al agregar la funcionalidad de Signalr a una aplicación ASP.NET Core, configure las rutas `endpoint.MapHub` de signalr llamando a en la `Startup.Configure` devolución de llamada del `app.UseEndpoints` método.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Al agregar la funcionalidad de Signalr a una aplicación ASP.NET Core, configure las rutas `app.UseSignalR` de signalr llamando a en el `Startup.Configure` método.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Crear y usar hubs

Cree un centro declarando una clase que herede de `Hub`y agréguele métodos públicos. Los clientes pueden llamar a métodos definidos como `public`.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Puede especificar un tipo de valor devuelto y parámetros, incluidos tipos complejos y matrices, como haría en cualquier método de C#. Signalr controla la serialización y deserialización de objetos y matrices complejos en los parámetros y valores devueltos.

> [!NOTE]
> Los concentradores son transitorios:
>
> * No almacene el estado en una propiedad de la clase hub. Cada llamada al método de concentrador se ejecuta en una nueva instancia del concentrador.
> * Se `await` usa al llamar a métodos asincrónicos que dependen de que el concentrador permanezca activo. Por ejemplo, un método como `Clients.All.SendAsync(...)` puede producir un error si se llama sin `await` y el método de concentrador se `SendAsync` completa antes de que finalice.

## <a name="the-context-object"></a>El objeto de contexto

La `Hub` clase tiene una `Context` propiedad que contiene las siguientes propiedades con información sobre la conexión:

| Propiedad. | Descripción |
| ------ | ----------- |
| `ConnectionId` | Obtiene el identificador único para la conexión, asignado por Signalr. Hay un identificador de conexión para cada conexión.|
| `UserIdentifier` | Obtiene el [identificador de usuario](xref:signalr/groups). De forma predeterminada, Signalr usa `ClaimTypes.NameIdentifier` el objeto `ClaimsPrincipal` de la clase asociada a la conexión como identificador de usuario. |
| `User` | Obtiene el `ClaimsPrincipal` objeto asociado al usuario actual. |
| `Items` | Obtiene una colección de clave/valor que se puede utilizar para compartir datos dentro del ámbito de esta conexión. Los datos se pueden almacenar en esta colección y se conservarán para la conexión entre las distintas invocaciones de métodos de concentrador. |
| `Features` | Obtiene la colección de características disponibles en la conexión. Por ahora, esta colección no es necesaria en la mayoría de los escenarios, por lo que aún no está documentada en detalle. |
| `ConnectionAborted` | Obtiene un `CancellationToken` que notifica cuando se anula la conexión. |

`Hub.Context`también contiene los métodos siguientes:

| Método | Descripción |
| ------ | ----------- |
| `GetHttpContext` | Devuelve el `HttpContext` objeto para la conexión o `null` si la conexión no está asociada a una solicitud HTTP. En el caso de las conexiones HTTP, puede usar este método para obtener información como encabezados HTTP y cadenas de consulta. |
| `Abort` | Anula la conexión. |

## <a name="the-clients-object"></a>Objeto clients

La `Hub` clase tiene una `Clients` propiedad que contiene las siguientes propiedades para la comunicación entre el servidor y el cliente:

| Propiedad. | Descripción |
| ------ | ----------- |
| `All` | Llama a un método en todos los clientes conectados |
| `Caller` | Llama a un método en el cliente que invocó el método de concentrador. |
| `Others` | Llama a un método en todos los clientes conectados, excepto el cliente que invocó el método. |

`Hub.Clients`también contiene los métodos siguientes:

| Método | Descripción |
| ------ | ----------- |
| `AllExcept` | Llama a un método en todos los clientes conectados, excepto las conexiones especificadas. |
| `Client` | Llama a un método en un cliente conectado específico |
| `Clients` | Llama a un método en clientes conectados específicos |
| `Group` | Llama a un método en todas las conexiones del grupo especificado  |
| `GroupExcept` | Llama a un método en todas las conexiones del grupo especificado, excepto las conexiones especificadas. |
| `Groups` | Llama a un método en varios grupos de conexiones  |
| `OthersInGroup` | Llama a un método en un grupo de conexiones, excluido el cliente que invocó el método de concentrador.  |
| `User` | Llama a un método en todas las conexiones asociadas a un usuario específico |
| `Users` | Llama a un método en todas las conexiones asociadas a los usuarios especificados |

Cada propiedad o método de las tablas anteriores devuelve un objeto con un `SendAsync` método. El `SendAsync` método permite proporcionar el nombre y los parámetros del método de cliente que se va a llamar.

## <a name="send-messages-to-clients"></a>Enviar mensajes a los clientes

Para realizar llamadas a clientes específicos, utilice las propiedades del `Clients` objeto. En el ejemplo siguiente, hay tres métodos de concentrador:

* `SendMessage`envía un mensaje a todos los clientes conectados mediante `Clients.All`.
* `SendMessageToCaller`Devuelve un mensaje al autor de la llamada mediante `Clients.Caller`.
* `SendMessageToGroups`envía un mensaje a todos los clientes del `SignalR Users` grupo.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Concentradores fuertemente tipados

Un inconveniente de `SendAsync` usar es que se basa en una cadena mágica para especificar el método de cliente al que se va a llamar. Esto deja el código abierto en los errores en tiempo de ejecución si el nombre del método está mal escrito o falta en el cliente.

Una alternativa al uso `SendAsync` de es para escribir fuertemente `Hub` con <xref:Microsoft.AspNetCore.SignalR.Hub%601>. En el ejemplo siguiente, los `ChatHub` métodos de cliente se han extraído en una interfaz `IChatClient`denominada.

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Esta interfaz se puede usar para refactorizar el ejemplo `ChatHub` anterior.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

El `Hub<IChatClient>` uso de habilita la comprobación en tiempo de compilación de los métodos de cliente. Esto evita problemas causados por el uso de cadenas mágicas, ya que `Hub<T>` solo puede proporcionar acceso a los métodos definidos en la interfaz.

El uso de un fuertemente `Hub<T>` tipado deshabilita la capacidad `SendAsync`de usar. Cualquier método definido en la interfaz todavía se puede definir como asincrónico. De hecho, cada uno de estos métodos debe devolver `Task`. Dado que es una interfaz, no use la `async` palabra clave. Por ejemplo:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> El `Async` sufijo no se elimina del nombre del método. A menos que el método de cliente `.on('MyMethodAsync')`se defina con, `MyMethodAsync` no se debe usar como nombre.

## <a name="change-the-name-of-a-hub-method"></a>Cambiar el nombre de un método de concentrador

De forma predeterminada, un nombre de método de concentrador de servidor es el nombre del método de .NET. Sin embargo, puede usar el atributo [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) para cambiar este valor predeterminado y especificar manualmente un nombre para el método. El cliente debe utilizar este nombre, en lugar del nombre del método .NET, al invocar el método.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Controlar eventos para una conexión

La SignalR API de hubs proporciona `OnConnectedAsync` los `OnDisconnectedAsync` métodos virtuales y para administrar y realizar un seguimiento de las conexiones. Invalide el `OnConnectedAsync` método virtual para realizar acciones cuando un cliente se conecta al centro, como agregarlo a un grupo.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Invalide el `OnDisconnectedAsync` método virtual para realizar acciones cuando un cliente se desconecte. Si el cliente se `connection.stop()` `exception` `null`desconecta intencionadamente (mediante una llamada a, por ejemplo), el parámetro será. Sin embargo, si el cliente está desconectado debido a un error (por ejemplo, un error de red) `exception` , el parámetro contendrá una excepción que describe el error.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Control de errores

Las excepciones iniciadas en los métodos de concentrador se envían al cliente que invocó el método. En el cliente de JavaScript, `invoke` el método devuelve un [compromiso de JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Cuando el cliente recibe un error con un controlador asociado a la promesa mediante `catch`, se invoca y se pasa como un objeto de `Error` JavaScript.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Si el centro inicia una excepción, las conexiones no se cierran. De forma predeterminada SignalR , devuelve un mensaje de error genérico al cliente. Por ejemplo:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Las excepciones inesperadas a menudo contienen información confidencial, como el nombre de un servidor de base de datos en una excepción que se desencadena cuando se produce un error en la conexión de base de datos. SignalRno expone estos mensajes de error detallados de forma predeterminada como medida de seguridad. Vea el [artículo sobre consideraciones de seguridad](xref:signalr/security#exceptions) para obtener más información sobre por qué se suprimen los detalles de la excepción.

Si tiene una condición *excepcional que desea* propagar al cliente, puede utilizar la `HubException` clase. Si inicia una `HubException` excepción desde el SignalR **método de concentrador,** enviará todo el mensaje al cliente, sin modificar.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalRsolo envía la `Message` propiedad de la excepción al cliente. El seguimiento de la pila y otras propiedades de la excepción no están disponibles para el cliente.

## <a name="related-resources"></a>Recursos relacionados

* [Introducción a ASP.NET CoreSignalR](xref:signalr/introduction)
* [Cliente de JavaScript](xref:signalr/javascript-client)
* [Publicación en Azure](xref:signalr/publish-to-azure-web-app)
