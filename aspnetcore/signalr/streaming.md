---
title: Usar streaming en ASP.NET CoreSignalR
author: bradygaster
description: Obtenga información acerca de cómo transmitir datos entre el cliente y el servidor.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 4d6461bc85573776ccdbe81bf3c74145a9cf7ed6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773895"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Usar streaming en ASP.NET CoreSignalR

Por [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR admite el streaming desde el cliente al servidor y desde el servidor al cliente. Esto resulta útil para escenarios en los que llegan fragmentos de datos a lo largo del tiempo. Al transmitir por secuencias, cada fragmento se envía al cliente o al servidor en cuanto está disponible, en lugar de esperar a que todos los datos estén disponibles.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR admite el streaming de valores devueltos de métodos de servidor. Esto resulta útil para escenarios en los que llegan fragmentos de datos a lo largo del tiempo. Cuando un valor devuelto se transmite al cliente, cada fragmento se envía al cliente en cuanto está disponible, en lugar de esperar a que todos los datos estén disponibles.

::: moniker-end

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Configuración de un centro para la transmisión por secuencias

::: moniker range=">= aspnetcore-3.0"

Un método de concentrador se convierte automáticamente en un método de <xref:System.Collections.Generic.IAsyncEnumerable`1>concentrador de streaming cuando devuelve, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`o `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un método de concentrador se convierte automáticamente en un método de concentrador de streaming cuando devuelve <xref:System.Threading.Channels.ChannelReader%601> o `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming de servidor a cliente

::: moniker range=">= aspnetcore-3.0"

Los métodos de streaming `IAsyncEnumerable<T>` Hub pueden devolver `ChannelReader<T>`además de. La manera más sencilla de devolver `IAsyncEnumerable<T>` es hacer que el método de concentrador sea un método de iterador asincrónico como se muestra en el ejemplo siguiente. Los métodos de iterador Async de `CancellationToken` concentrador pueden aceptar un parámetro que se desencadena cuando el cliente cancela la suscripción de la secuencia. Los métodos de iterador asincrónico evitan problemas comunes con los canales, `ChannelReader` como no devolver el tiempo suficiente o salir del método <xref:System.Threading.Channels.ChannelWriter`1>sin completar el.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

En el ejemplo siguiente se muestran los aspectos básicos de la transmisión de datos al cliente mediante canales. Siempre que se escribe un objeto en <xref:System.Threading.Channels.ChannelWriter%601>, el objeto se envía inmediatamente al cliente. Al final, `ChannelWriter` se completa para indicar al cliente que la secuencia está cerrada.

> [!NOTE]
> Escriba en `ChannelWriter<T>` en un subproceso en segundo plano y `ChannelReader` devuelva lo antes posible. Otras invocaciones del concentrador se bloquean `ChannelReader` hasta que se devuelve un.
>
> Ajusta la lógica en `try ... catch`un. Complete el `Channel` en `catch` y fuera de `catch` para asegurarse de que la invocación del método del concentrador se ha completado correctamente.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Los métodos del concentrador de streaming de servidor a `CancellationToken` cliente pueden aceptar un parámetro que se desencadena cuando el cliente cancela la suscripción a la secuencia. Use este token para detener la operación del servidor y liberar los recursos si el cliente se desconecta antes del final de la secuencia.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente a servidor

Un método de concentrador se convierte automáticamente en un método de concentrador de streaming de cliente a servidor cuando acepta <xref:System.Threading.Channels.ChannelReader%601> uno <xref:System.Collections.Generic.IAsyncEnumerable%601>o más objetos de tipo o. En el ejemplo siguiente se muestran los aspectos básicos de la lectura de datos de streaming enviados desde el cliente. Cada vez que el cliente escribe <xref:System.Threading.Channels.ChannelWriter%601>en, los datos se escriben `ChannelReader` en en el servidor desde el que el método de concentrador está leyendo.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

A <xref:System.Collections.Generic.IAsyncEnumerable%601> continuación se muestra una versión del método.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>Cliente .NET

### <a name="server-to-client-streaming"></a>Streaming de servidor a cliente


::: moniker range=">= aspnetcore-3.0"

Los `StreamAsync` métodos `StreamAsChannelAsync` y en `HubConnection` se usan para invocar métodos de streaming de servidor a cliente. Pase el nombre del método de concentrador y los argumentos definidos en `StreamAsync` el `StreamAsChannelAsync`método de concentrador a o. El parámetro genérico en `StreamAsync<T>` y `StreamAsChannelAsync<T>` especifica el tipo de objetos devueltos por el método de transmisión por secuencias. Un objeto de tipo `IAsyncEnumerable<T>` o `ChannelReader<T>` se devuelve de la invocación de flujo y representa la secuencia en el cliente.

Un `StreamAsync` ejemplo que devuelve `IAsyncEnumerable<int>`:

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

Un ejemplo `StreamAsChannelAsync` correspondiente que devuelve `ChannelReader<int>`:

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

El `StreamAsChannelAsync` método on `HubConnection` se usa para invocar un método de streaming de servidor a cliente. Pase el nombre del método de concentrador y los argumentos definidos en `StreamAsChannelAsync`el método de concentrador a. El parámetro Generic en `StreamAsChannelAsync<T>` especifica el tipo de objetos devueltos por el método de transmisión por secuencias. Se `ChannelReader<T>` devuelve un objeto de la invocación de flujo y representa la secuencia en el cliente.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

El `StreamAsChannelAsync` método on `HubConnection` se usa para invocar un método de streaming de servidor a cliente. Pase el nombre del método de concentrador y los argumentos definidos en `StreamAsChannelAsync`el método de concentrador a. El parámetro Generic en `StreamAsChannelAsync<T>` especifica el tipo de objetos devueltos por el método de transmisión por secuencias. Se `ChannelReader<T>` devuelve un objeto de la invocación de flujo y representa la secuencia en el cliente.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente a servidor

Hay dos maneras de invocar un método de concentrador de streaming de cliente a servidor desde el cliente .NET. `IAsyncEnumerable<T>` Puede pasar o `ChannelReader` como un argumento a `SendAsync`, `InvokeAsync`o `StreamAsChannelAsync`, dependiendo del método de concentrador invocado.

Cada vez que se escriben `IAsyncEnumerable` datos `ChannelWriter` en el objeto o, el método de concentrador del servidor recibe un nuevo elemento con los datos del cliente.

Si se usa `IAsyncEnumerable` un objeto, el flujo termina después de que se cierre el método que devuelve los elementos de la secuencia.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

O bien, si está usando `ChannelWriter`un, complete el canal con `channel.Writer.Complete()`:

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Cliente de JavaScript

### <a name="server-to-client-streaming"></a>Streaming de servidor a cliente

Los clientes de JavaScript llaman a los métodos de streaming de servidor `connection.stream`a cliente en los concentradores con. El `stream` método acepta dos argumentos:

* Nombre del método de concentrador. En el ejemplo siguiente, el nombre del método del `Counter`concentrador es.
* Argumentos definidos en el método de concentrador. En el ejemplo siguiente, los argumentos son un recuento del número de elementos de secuencia que se van a recibir y el retraso entre los elementos de la secuencia.

`connection.stream`Devuelve un `IStreamResult`, que contiene un `subscribe` método. Pase `IStreamSubscriber` a `subscribe` y establezca las `next`devoluciones de llamada, `error`y `complete` para recibir notificaciones de `stream` la invocación.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Para finalizar la secuencia del cliente, llame al `dispose` método en el `ISubscription` que se devuelve desde el `subscribe` método. La llamada a este método provoca la `CancellationToken` cancelación del parámetro del método de concentrador, si se proporcionó uno.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Para finalizar la secuencia del cliente, llame al `dispose` método en el `ISubscription` que se devuelve desde el `subscribe` método.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming de cliente a servidor

Los clientes de JavaScript llaman a métodos de streaming de cliente a servidor en los `Subject` concentradores pasando un `send`como `invoke`argumento a `stream`, o, dependiendo del método de concentrador invocado. Es `Subject` una clase que tiene el aspecto de `Subject`un. Por ejemplo, en RxJS, puede usar la clase de [asunto](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) de la biblioteca.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

La `subject.next(item)` llamada a con un elemento escribe el elemento en la secuencia y el método de concentrador recibe el elemento en el servidor.

Para finalizar el flujo, llame `subject.complete()`a.

## <a name="java-client"></a>Cliente de Java

### <a name="server-to-client-streaming"></a>Streaming de servidor a cliente

El SignalR cliente de Java usa `stream` el método para invocar métodos de streaming. `stream`acepta tres o más argumentos:

* Tipo esperado de los elementos de la secuencia.
* Nombre del método de concentrador.
* Argumentos definidos en el método de concentrador.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

El `stream` método en `HubConnection` devuelve un objeto observable del tipo de elemento de secuencia. El método del `subscribe` tipo observable es donde `onNext`, `onError` y `onCompleted` se definen los controladores.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* [Concentradores](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Cliente de JavaScript](xref:signalr/javascript-client)
* [Publicación en Azure](xref:signalr/publish-to-azure-web-app)
