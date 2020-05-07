---
title: Compatibilidad con WebSockets en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo empezar a usar WebSockets en ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: da713f22582cf17f60a4deda1b689662a4e4ae06
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775445"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="b5971-103">Compatibilidad con WebSockets en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5971-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="b5971-104">Por [Tom Dykstra](https://github.com/tdykstra) y [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b5971-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="b5971-105">En este artículo se ofrece una introducción a WebSockets en ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5971-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="b5971-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) es un protocolo que habilita canales de comunicación bidireccional persistentes a través de conexiones TCP.</span><span class="sxs-lookup"><span data-stu-id="b5971-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="b5971-107">Se usa en aplicaciones que sacan partido de comunicaciones rápidas y en tiempo real, como las aplicaciones de chat, panel y juegos.</span><span class="sxs-lookup"><span data-stu-id="b5971-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="b5971-108">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b5971-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b5971-109">[Cómo ejecutar](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="b5971-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="b5971-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="b5971-110">SignalR</span></span>

<span data-ttu-id="b5971-111">[SignalR de ASP.NET Core](xref:signalr/introduction) es una biblioteca que simplifica la adición de la funcionalidad web en tiempo real a las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="b5971-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="b5971-112">Usa WebSockets siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="b5971-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="b5971-113">Para la mayoría de las aplicaciones, se recomienda SignalR en lugar de WebSockets sin procesar.</span><span class="sxs-lookup"><span data-stu-id="b5971-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="b5971-114">SignalR proporciona transporte de reserva para entornos donde WebSockets no está disponible.</span><span class="sxs-lookup"><span data-stu-id="b5971-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="b5971-115">También proporciona un modelo simple de aplicaciones de llamada a procedimiento remoto.</span><span class="sxs-lookup"><span data-stu-id="b5971-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="b5971-116">Y, en la mayoría de los escenarios, SignalR no tiene ninguna desventaja significativa de rendimiento en comparación a los WebSockets sin procesar.</span><span class="sxs-lookup"><span data-stu-id="b5971-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b5971-117">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="b5971-117">Prerequisites</span></span>

* <span data-ttu-id="b5971-118">ASP.NET Core 1.1 o posterior</span><span class="sxs-lookup"><span data-stu-id="b5971-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="b5971-119">Cualquier sistema operativo que admita ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b5971-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="b5971-120">Windows 7/Windows Server 2008 o posterior</span><span class="sxs-lookup"><span data-stu-id="b5971-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="b5971-121">Linux</span><span class="sxs-lookup"><span data-stu-id="b5971-121">Linux</span></span>
  * <span data-ttu-id="b5971-122">macOS</span><span class="sxs-lookup"><span data-stu-id="b5971-122">macOS</span></span>
  
* <span data-ttu-id="b5971-123">Si la aplicación se ejecuta en Windows con IIS:</span><span class="sxs-lookup"><span data-stu-id="b5971-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="b5971-124">Windows 8/Windows Server 2012 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="b5971-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="b5971-125">IIS 8/Express IIS 8</span><span class="sxs-lookup"><span data-stu-id="b5971-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="b5971-126">WebSockets debe estar habilitado (consulte la sección [Compatibilidad con IIS/IIS Express](#iisiis-express-support)).</span><span class="sxs-lookup"><span data-stu-id="b5971-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="b5971-127">Si la aplicación se ejecuta en [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="b5971-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="b5971-128">Windows 8/Windows Server 2012 o versiones posteriores</span><span class="sxs-lookup"><span data-stu-id="b5971-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="b5971-129">Para saber qué exploradores son compatibles, vea https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="b5971-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="b5971-130">Detección de</span><span class="sxs-lookup"><span data-stu-id="b5971-130">NuGet package</span></span>

<span data-ttu-id="b5971-131">Instale el paquete [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/).</span><span class="sxs-lookup"><span data-stu-id="b5971-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="b5971-132">Configurar el middleware</span><span class="sxs-lookup"><span data-stu-id="b5971-132">Configure the middleware</span></span>


<span data-ttu-id="b5971-133">Agregue el middleware de WebSockets al método `Configure` de la clase `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b5971-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b5971-134">Se pueden configurar estas opciones:</span><span class="sxs-lookup"><span data-stu-id="b5971-134">The following settings can be configured:</span></span>

* <span data-ttu-id="b5971-135">`KeepAliveInterval`: la frecuencia con que se envían marcos "ping" al cliente, para asegurarse de que los servidores proxy mantienen abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b5971-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="b5971-136">El valor predeterminado es de dos minutos.</span><span class="sxs-lookup"><span data-stu-id="b5971-136">The default is two minutes.</span></span>
* <span data-ttu-id="b5971-137">`ReceiveBufferSize`: el tamaño del búfer usado para recibir datos.</span><span class="sxs-lookup"><span data-stu-id="b5971-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="b5971-138">Puede que los usuarios avanzados tengan que cambiar estas opciones para ajustar el rendimiento según el tamaño de los datos.</span><span class="sxs-lookup"><span data-stu-id="b5971-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="b5971-139">El valor predeterminado es 4 KB.</span><span class="sxs-lookup"><span data-stu-id="b5971-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b5971-140">Se pueden configurar estas opciones:</span><span class="sxs-lookup"><span data-stu-id="b5971-140">The following settings can be configured:</span></span>

* <span data-ttu-id="b5971-141">`KeepAliveInterval`: la frecuencia con que se envían marcos "ping" al cliente, para asegurarse de que los servidores proxy mantienen abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="b5971-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="b5971-142">El valor predeterminado es de dos minutos.</span><span class="sxs-lookup"><span data-stu-id="b5971-142">The default is two minutes.</span></span>
* <span data-ttu-id="b5971-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>: el tamaño del búfer usado para recibir datos.</span><span class="sxs-lookup"><span data-stu-id="b5971-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="b5971-144">Puede que los usuarios avanzados tengan que cambiar estas opciones para ajustar el rendimiento según el tamaño de los datos.</span><span class="sxs-lookup"><span data-stu-id="b5971-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="b5971-145">El valor predeterminado es 4 KB.</span><span class="sxs-lookup"><span data-stu-id="b5971-145">The default is 4 KB.</span></span>
* <span data-ttu-id="b5971-146">`AllowedOrigins` - Una lista de valores de encabezado de origen permitidos para las solicitudes WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b5971-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="b5971-147">De forma predeterminada, se permiten todos los orígenes.</span><span class="sxs-lookup"><span data-stu-id="b5971-147">By default, all origins are allowed.</span></span> <span data-ttu-id="b5971-148">Consulte "Restricción de los orígenes de WebSocket" a continuación para obtener información detallada.</span><span class="sxs-lookup"><span data-stu-id="b5971-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="b5971-149">Aceptar solicitudes WebSocket</span><span class="sxs-lookup"><span data-stu-id="b5971-149">Accept WebSocket requests</span></span>

<span data-ttu-id="b5971-150">En algún momento posterior del ciclo de solicitudes (más adelante en el método `Configure` o en un método de acción, por ejemplo) debe comprobar si se trata de una solicitud WebSocket y aceptarla.</span><span class="sxs-lookup"><span data-stu-id="b5971-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="b5971-151">El siguiente ejemplo se corresponde con un momento más adelante en el método `Configure`:</span><span class="sxs-lookup"><span data-stu-id="b5971-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="b5971-152">Una solicitud WebSocket puede proceder de cualquier dirección URL, pero este código de ejemplo solo acepta solicitudes de `/ws`.</span><span class="sxs-lookup"><span data-stu-id="b5971-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="b5971-153">Cuando se usa un WebSocket, **debe** mantener la canalización de middleware en ejecución durante la duración de la conexión.</span><span class="sxs-lookup"><span data-stu-id="b5971-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="b5971-154">Si intenta enviar o recibir un mensaje de WebSocket después de que finalice la canalización de middleware, es posible que obtenga una excepción como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="b5971-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="b5971-155">Si utiliza un servicio en segundo plano para escribir datos en un WebSocket, asegúrese de mantener en ejecución el canal de middleware.</span><span class="sxs-lookup"><span data-stu-id="b5971-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="b5971-156">Para ello, utilice un <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="b5971-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="b5971-157">Pase `TaskCompletionSource` a su servicio de segundo plano y pídale que llame a <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> cuando termine con WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b5971-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="b5971-158">Después, espere (con `await`) la propiedad <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> durante la solicitud, como se muestra en el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b5971-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="b5971-159">La excepción de cierre de WebSocket también puede ocurrir si la devolución de un método de acción se produce demasiado pronto.</span><span class="sxs-lookup"><span data-stu-id="b5971-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="b5971-160">Si acepta un socket en un método de acción, espere a que finalice el código que utiliza el socket antes de volver del método de acción.</span><span class="sxs-lookup"><span data-stu-id="b5971-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="b5971-161">No use nunca `Task.Wait()`, `Task.Result` ni llamadas de bloqueo similares para esperar a que se complete el socket, ya que pueden causar graves problemas de subprocesamiento.</span><span class="sxs-lookup"><span data-stu-id="b5971-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="b5971-162">Use siempre `await`.</span><span class="sxs-lookup"><span data-stu-id="b5971-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="b5971-163">Enviar y recibir mensajes</span><span class="sxs-lookup"><span data-stu-id="b5971-163">Send and receive messages</span></span>

<span data-ttu-id="b5971-164">El método `AcceptWebSocketAsync` actualiza la conexión TCP a una conexión WebSocket y proporciona un objeto [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="b5971-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="b5971-165">Use el objeto `WebSocket` para enviar y recibir mensajes.</span><span class="sxs-lookup"><span data-stu-id="b5971-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="b5971-166">El código antes mostrado que acepta la solicitud WebSocket pasa el objeto `WebSocket` a un método `Echo`.</span><span class="sxs-lookup"><span data-stu-id="b5971-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="b5971-167">El código recibe un mensaje y devuelve inmediatamente el mismo mensaje.</span><span class="sxs-lookup"><span data-stu-id="b5971-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="b5971-168">Los mensajes se envían y reciben en un bucle hasta que el cliente cierra la conexión:</span><span class="sxs-lookup"><span data-stu-id="b5971-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="b5971-169">Cuando la conexión WebSocket se acepta antes de que el bucle comience, la canalización de middleware finaliza.</span><span class="sxs-lookup"><span data-stu-id="b5971-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="b5971-170">Tras cerrar el socket, se desenreda la canalización.</span><span class="sxs-lookup"><span data-stu-id="b5971-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="b5971-171">Es decir, la solicitud deja de avanzar en la canalización cuando WebSocket se acepta,</span><span class="sxs-lookup"><span data-stu-id="b5971-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="b5971-172">pero cuando el bucle termina y el socket se cierra, la solicitud vuelve a recorrer la canalización.</span><span class="sxs-lookup"><span data-stu-id="b5971-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="b5971-173">Control de las desconexiones del cliente</span><span class="sxs-lookup"><span data-stu-id="b5971-173">Handle client disconnects</span></span>

<span data-ttu-id="b5971-174">No se informa automáticamente al servidor cuando el cliente se desconecta debido a la pérdida de conectividad.</span><span class="sxs-lookup"><span data-stu-id="b5971-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="b5971-175">El servidor recibe un mensaje de desconexión solo si el cliente lo envía, acción que no se puede realizar si se pierde la conexión a Internet.</span><span class="sxs-lookup"><span data-stu-id="b5971-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="b5971-176">Si desea realizar alguna acción cuando eso suceda, establezca un tiempo de expiración después de que no se reciba del cliente dentro de un determinado período.</span><span class="sxs-lookup"><span data-stu-id="b5971-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="b5971-177">Si el cliente no siempre está enviando mensajes y no quiere que se agote el tiempo de expiración solo porque la conexión está inactiva, haga que el cliente utilice un temporizador para enviar un mensaje de ping cada equis segundos.</span><span class="sxs-lookup"><span data-stu-id="b5971-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="b5971-178">En el servidor, si aún no ha llegado un mensaje dentro de 2\*X segundos después del anterior, termine la conexión e informe que ha desconectado el cliente.</span><span class="sxs-lookup"><span data-stu-id="b5971-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="b5971-179">Espere el doble del intervalo de tiempo esperado para dejar tiempo extra para los retrasos de la red que podrían retener el mensaje de ping.</span><span class="sxs-lookup"><span data-stu-id="b5971-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="b5971-180">Restricción de los orígenes de WebSocket</span><span class="sxs-lookup"><span data-stu-id="b5971-180">WebSocket origin restriction</span></span>

<span data-ttu-id="b5971-181">Las protecciones proporcionadas por CORS no se aplican a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b5971-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b5971-182">Los exploradores **no** hacen lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b5971-182">Browsers do **not**:</span></span>

* <span data-ttu-id="b5971-183">Efectúan solicitudes preparatorias CORS.</span><span class="sxs-lookup"><span data-stu-id="b5971-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="b5971-184">Respetan las restricciones especificadas en los encabezados `Access-Control` al efectuar solicitudes de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b5971-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="b5971-185">En cambio, sí que envían el encabezado `Origin` al emitir solicitudes de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b5971-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="b5971-186">Las aplicaciones deben configurarse para validar estos encabezados a fin de garantizar que solo se permitan los WebSockets procedentes de los orígenes esperados.</span><span class="sxs-lookup"><span data-stu-id="b5971-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="b5971-187">Si hospeda su servidor en "https://server.com" y su cliente en "https://client.com", agregue "https://client.com" a la lista `AllowedOrigins` de WebSockets para efectuar la comprobación.</span><span class="sxs-lookup"><span data-stu-id="b5971-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="b5971-188">El encabezado `Origin` está controlado por el cliente y, al igual que el encabezado `Referer`, se puede falsificar.</span><span class="sxs-lookup"><span data-stu-id="b5971-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="b5971-189">**No** use estos encabezados como mecanismo de autenticación.</span><span class="sxs-lookup"><span data-stu-id="b5971-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="b5971-190">Compatibilidad con IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="b5971-190">IIS/IIS Express support</span></span>

<span data-ttu-id="b5971-191">El protocolo WebSocket se puede usar en Windows Server 2012 o posterior, y en Windows 8 o posterior con IIS o IIS Express 8 o posterior.</span><span class="sxs-lookup"><span data-stu-id="b5971-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="b5971-192">WebSocket siempre está habilitado cuando se usa IIS Express.</span><span class="sxs-lookup"><span data-stu-id="b5971-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="b5971-193">Habilitación de WebSocket en IIS</span><span class="sxs-lookup"><span data-stu-id="b5971-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="b5971-194">Para habilitar la compatibilidad con el protocolo WebSocket en Windows Server 2012 o posterior:</span><span class="sxs-lookup"><span data-stu-id="b5971-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="b5971-195">Estos pasos no son necesarios cuando se usa IIS Express</span><span class="sxs-lookup"><span data-stu-id="b5971-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="b5971-196">Use el asistente **Agregar roles y características** del menú **Administrar** o el vínculo de **Administrador del servidor**.</span><span class="sxs-lookup"><span data-stu-id="b5971-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="b5971-197">Seleccione **Instalación basada en características o en roles**.</span><span class="sxs-lookup"><span data-stu-id="b5971-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="b5971-198">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b5971-198">Select **Next**.</span></span>
1. <span data-ttu-id="b5971-199">Seleccione el servidor que corresponda (el servidor local está seleccionado de forma predeterminada).</span><span class="sxs-lookup"><span data-stu-id="b5971-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="b5971-200">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b5971-200">Select **Next**.</span></span>
1. <span data-ttu-id="b5971-201">Expanda **Servidor web (IIS)** en el árbol **Roles**, expanda **Servidor web** y, por último, expanda **Desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="b5971-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="b5971-202">Seleccione **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="b5971-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="b5971-203">Seleccione **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b5971-203">Select **Next**.</span></span>
1. <span data-ttu-id="b5971-204">Si no necesita más características, haga clic en **Siguiente**.</span><span class="sxs-lookup"><span data-stu-id="b5971-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="b5971-205">Haga clic en **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="b5971-205">Select **Install**.</span></span>
1. <span data-ttu-id="b5971-206">Cuando la instalación finalice, haga clic en **Cerrar** para salir del asistente.</span><span class="sxs-lookup"><span data-stu-id="b5971-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="b5971-207">Para habilitar la compatibilidad con el protocolo WebSocket en Windows Server 8 o posterior:</span><span class="sxs-lookup"><span data-stu-id="b5971-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="b5971-208">Estos pasos no son necesarios cuando se usa IIS Express</span><span class="sxs-lookup"><span data-stu-id="b5971-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="b5971-209">Vaya a **Panel de control** > **Programas** > **Programas y características** > **Activar o desactivar las características de Windows** (lado izquierdo de la pantalla).</span><span class="sxs-lookup"><span data-stu-id="b5971-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="b5971-210">Abra los nodos siguientes: **Internet Information Services** > **Servicios World Wide Web** > **Características de desarrollo de aplicaciones**.</span><span class="sxs-lookup"><span data-stu-id="b5971-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="b5971-211">Seleccione la característica **Protocolo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="b5971-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="b5971-212">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="b5971-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="b5971-213">Deshabilitar WebSocket al usar socket.io en Node.js</span><span class="sxs-lookup"><span data-stu-id="b5971-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="b5971-214">Si usa la compatibilidad de WebSocket en [socket.io](https://socket.io/) en [Node.js](https://nodejs.org/), deshabilite el módulo IIS WebSocket predeterminado, usando para ello el elemento `webSocket` de *web.config* o de *applicationHost.config*. Si este paso no se lleva a cabo, el módulo IIS WebSocket intenta controlar la comunicación de WebSocket en lugar de Node.js y la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b5971-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="b5971-215">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="b5971-215">Sample app</span></span>

<span data-ttu-id="b5971-216">La [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) que acompaña a este artículo es una aplicación de eco.</span><span class="sxs-lookup"><span data-stu-id="b5971-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="b5971-217">Tiene una página web que realiza las conexiones WebSocket y el servidor reenvía de vuelta al cliente todos los mensajes que reciba.</span><span class="sxs-lookup"><span data-stu-id="b5971-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="b5971-218">Ejecute la aplicación desde un símbolo del sistema (no está configurada para ejecutarse desde Visual Studio con IIS Express) y vaya a http://localhost:5000.</span><span class="sxs-lookup"><span data-stu-id="b5971-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="b5971-219">En la página web se muestra el estado de conexión en la parte superior izquierda:</span><span class="sxs-lookup"><span data-stu-id="b5971-219">The web page shows the connection status in the upper left:</span></span>

![Estado inicial de la página web](websockets/_static/start.png)

<span data-ttu-id="b5971-221">Seleccione **Connect** (Conectar) para enviar una solicitud WebSocket para la URL mostrada.</span><span class="sxs-lookup"><span data-stu-id="b5971-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="b5971-222">Escriba un mensaje de prueba y seleccione **Send** (Enviar).</span><span class="sxs-lookup"><span data-stu-id="b5971-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="b5971-223">Cuando haya terminado, seleccione **Close Socket** (Cerrar socket).</span><span class="sxs-lookup"><span data-stu-id="b5971-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="b5971-224">Los informes de la sección **Communication Log** (Registro de comunicación) informan de cada acción de abrir, enviar y cerrar a medida que se producen.</span><span class="sxs-lookup"><span data-stu-id="b5971-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Estado inicial de la página web](websockets/_static/end.png)

