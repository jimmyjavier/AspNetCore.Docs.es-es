---
title: Configuración de ASP.NET Core SignalR
author: bradygaster
description: Aprenda a configurar ASP.NET Core SignalR aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 09866f1fd56a4d0747ef3814c85ab5070cfb8d59
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756124"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="5ec61-103">Configuración de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="5ec61-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5ec61-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-105">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="5ec61-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5ec61-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="5ec61-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5ec61-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="5ec61-108">`AddJsonProtocol`se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ec61-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="5ec61-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5ec61-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5ec61-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5ec61-111">Para obtener más información, consulte la [documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="5ec61-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="5ec61-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="5ec61-113">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ec61-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5ec61-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5ec61-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="5ec61-116">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="5ec61-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="5ec61-117">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="5ec61-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5ec61-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-118">MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5ec61-120">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5ec61-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="5ec61-122">Configure server options</span></span>

<span data-ttu-id="5ec61-123">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="5ec61-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5ec61-124">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-124">Option</span></span> | <span data-ttu-id="5ec61-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-125">Default Value</span></span> | <span data-ttu-id="5ec61-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5ec61-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-127">30 seconds</span></span> | <span data-ttu-id="5ec61-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5ec61-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5ec61-130">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5ec61-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-131">15 seconds</span></span> | <span data-ttu-id="5ec61-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="5ec61-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5ec61-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-134">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-135">15 seconds</span></span> | <span data-ttu-id="5ec61-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5ec61-137">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5ec61-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5ec61-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="5ec61-139">All installed protocols</span></span> | <span data-ttu-id="5ec61-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-140">Protocols supported by this hub.</span></span> <span data-ttu-id="5ec61-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="5ec61-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5ec61-142">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5ec61-143">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="5ec61-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="5ec61-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="5ec61-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="5ec61-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-146">32 KB</span></span> | <span data-ttu-id="5ec61-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="5ec61-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="5ec61-148">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5ec61-149">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="5ec61-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5ec61-150">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="5ec61-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5ec61-151">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5ec61-152">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="5ec61-153">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="5ec61-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5ec61-154">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-154">Option</span></span> | <span data-ttu-id="5ec61-155">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-155">Default Value</span></span> | <span data-ttu-id="5ec61-156">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5ec61-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-157">32 KB</span></span> | <span data-ttu-id="5ec61-158">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="5ec61-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="5ec61-159">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5ec61-160">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="5ec61-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5ec61-161">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5ec61-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-162">32 KB</span></span> | <span data-ttu-id="5ec61-163">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="5ec61-164">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5ec61-165">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-165">All Transports are enabled.</span></span> | <span data-ttu-id="5ec61-166">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="5ec61-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5ec61-167">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-167">See below.</span></span> | <span data-ttu-id="5ec61-168">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5ec61-169">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-169">See below.</span></span> | <span data-ttu-id="5ec61-170">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="5ec61-171">0</span><span class="sxs-lookup"><span data-stu-id="5ec61-171">0</span></span> | <span data-ttu-id="5ec61-172">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="5ec61-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="5ec61-173">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="5ec61-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="5ec61-174">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5ec61-175">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-175">Option</span></span> | <span data-ttu-id="5ec61-176">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-176">Default Value</span></span> | <span data-ttu-id="5ec61-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5ec61-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-178">90 seconds</span></span> | <span data-ttu-id="5ec61-179">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5ec61-180">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5ec61-181">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5ec61-182">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-182">Option</span></span> | <span data-ttu-id="5ec61-183">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-183">Default Value</span></span> | <span data-ttu-id="5ec61-184">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5ec61-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-185">5 seconds</span></span> | <span data-ttu-id="5ec61-186">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5ec61-187">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5ec61-188">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5ec61-189">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="5ec61-189">Configure client options</span></span>

<span data-ttu-id="5ec61-190">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5ec61-191">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="5ec61-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5ec61-192">registro</span><span class="sxs-lookup"><span data-stu-id="5ec61-192">Configure logging</span></span>

<span data-ttu-id="5ec61-193">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5ec61-194">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5ec61-195">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="5ec61-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-196">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="5ec61-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5ec61-197">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5ec61-198">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5ec61-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5ec61-199">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5ec61-200">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="5ec61-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5ec61-201">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="5ec61-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5ec61-202">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="5ec61-203">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="5ec61-204">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="5ec61-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="5ec61-205">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-205">The following table lists the available log levels.</span></span> <span data-ttu-id="5ec61-206">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="5ec61-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="5ec61-207">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="5ec61-208">String</span><span class="sxs-lookup"><span data-stu-id="5ec61-208">String</span></span>                      | <span data-ttu-id="5ec61-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="5ec61-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="5ec61-210">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="5ec61-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="5ec61-211">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="5ec61-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="5ec61-212">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5ec61-213">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="5ec61-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5ec61-214">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5ec61-215">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="5ec61-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5ec61-216">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="5ec61-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5ec61-217">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="5ec61-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5ec61-218">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5ec61-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="5ec61-219">Configure allowed transports</span></span>

<span data-ttu-id="5ec61-220">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5ec61-221">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5ec61-222">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ec61-222">All transports are enabled by default.</span></span>

<span data-ttu-id="5ec61-223">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="5ec61-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5ec61-224">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5ec61-225">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="5ec61-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="5ec61-226">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="5ec61-227">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5ec61-228">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="5ec61-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5ec61-229">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="5ec61-229">Configure bearer authentication</span></span>

<span data-ttu-id="5ec61-230">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5ec61-231">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="5ec61-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5ec61-232">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5ec61-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5ec61-233">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5ec61-234">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5ec61-235">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="5ec61-236">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5ec61-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5ec61-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="5ec61-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5ec61-238">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5ec61-239">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="5ec61-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5ec61-240">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-241">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-242">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-242">Option</span></span> | <span data-ttu-id="5ec61-243">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-243">Default value</span></span> | <span data-ttu-id="5ec61-244">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5ec61-245">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-246">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-246">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-247">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-248">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-249">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5ec61-250">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-250">15 seconds</span></span> | <span data-ttu-id="5ec61-251">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-252">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-253">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-254">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-255">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-255">15 seconds</span></span> | <span data-ttu-id="5ec61-256">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-257">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-258">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5ec61-259">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="5ec61-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5ec61-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-261">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-261">Option</span></span> | <span data-ttu-id="5ec61-262">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-262">Default value</span></span> | <span data-ttu-id="5ec61-263">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5ec61-264">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-265">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-265">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-266">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5ec61-267">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-268">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5ec61-269">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-270">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-271">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-272">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-273">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-273">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-274">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-274">Option</span></span> | <span data-ttu-id="5ec61-275">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-275">Default value</span></span> | <span data-ttu-id="5ec61-276">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5ec61-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5ec61-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5ec61-278">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-279">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-279">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-280">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-281">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-282">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5ec61-283">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-283">15 seconds</span></span> | <span data-ttu-id="5ec61-284">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-285">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-286">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-287">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5ec61-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5ec61-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5ec61-289">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-290">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-291">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-292">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5ec61-293">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-293">Configure additional options</span></span>

<span data-ttu-id="5ec61-294">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="5ec61-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-295">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-296">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-296">.NET Option</span></span> |  <span data-ttu-id="5ec61-297">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-297">Default value</span></span> | <span data-ttu-id="5ec61-298">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5ec61-299">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5ec61-300">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-301">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-302">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5ec61-303">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-303">Empty</span></span> | <span data-ttu-id="5ec61-304">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5ec61-305">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-305">Empty</span></span> | <span data-ttu-id="5ec61-306">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5ec61-307">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-307">Empty</span></span> | <span data-ttu-id="5ec61-308">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5ec61-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-309">5 seconds</span></span> | <span data-ttu-id="5ec61-310">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-310">WebSockets only.</span></span> <span data-ttu-id="5ec61-311">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="5ec61-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5ec61-312">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="5ec61-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5ec61-313">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-313">Empty</span></span> | <span data-ttu-id="5ec61-314">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5ec61-315">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5ec61-316">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="5ec61-317">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5ec61-318">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5ec61-319">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="5ec61-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5ec61-320">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5ec61-321">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5ec61-322">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="5ec61-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5ec61-323">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5ec61-324">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="5ec61-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5ec61-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-326">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-326">JavaScript Option</span></span> | <span data-ttu-id="5ec61-327">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-327">Default Value</span></span> | <span data-ttu-id="5ec61-328">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5ec61-329">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5ec61-330">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-331">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-332">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="5ec61-333">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="5ec61-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="5ec61-334">Azure App Service usa cookies para sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="5ec61-335">Para obtener más información sobre CORS con SignalR , vea <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="5ec61-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-336">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-336">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-337">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-337">Java Option</span></span> | <span data-ttu-id="5ec61-338">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-338">Default Value</span></span> | <span data-ttu-id="5ec61-339">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5ec61-340">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5ec61-341">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-342">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-343">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5ec61-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5ec61-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5ec61-345">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-345">Empty</span></span> | <span data-ttu-id="5ec61-346">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5ec61-347">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5ec61-348">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5ec61-349">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5ec61-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5ec61-350">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5ec61-351">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-352">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="5ec61-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5ec61-353">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="5ec61-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5ec61-354">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="5ec61-355">`AddJsonProtocol`se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ec61-356">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="5ec61-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5ec61-357">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5ec61-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5ec61-358">Para obtener más información, consulte la [documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="5ec61-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="5ec61-359">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="5ec61-360">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ec61-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5ec61-361">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5ec61-362">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="5ec61-363">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="5ec61-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="5ec61-364">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="5ec61-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5ec61-365">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-365">MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-366">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5ec61-367">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-368">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5ec61-369">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="5ec61-369">Configure server options</span></span>

<span data-ttu-id="5ec61-370">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="5ec61-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5ec61-371">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-371">Option</span></span> | <span data-ttu-id="5ec61-372">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-372">Default Value</span></span> | <span data-ttu-id="5ec61-373">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5ec61-374">30 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-374">30 seconds</span></span> | <span data-ttu-id="5ec61-375">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5ec61-376">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5ec61-377">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5ec61-378">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-378">15 seconds</span></span> | <span data-ttu-id="5ec61-379">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="5ec61-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5ec61-380">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-381">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-382">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-382">15 seconds</span></span> | <span data-ttu-id="5ec61-383">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5ec61-384">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5ec61-385">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5ec61-386">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="5ec61-386">All installed protocols</span></span> | <span data-ttu-id="5ec61-387">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-387">Protocols supported by this hub.</span></span> <span data-ttu-id="5ec61-388">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="5ec61-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5ec61-389">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5ec61-390">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="5ec61-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="5ec61-391">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="5ec61-392">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="5ec61-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-393">32 KB</span></span> | <span data-ttu-id="5ec61-394">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="5ec61-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="5ec61-395">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5ec61-396">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="5ec61-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5ec61-397">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="5ec61-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5ec61-398">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5ec61-399">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="5ec61-400">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="5ec61-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5ec61-401">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-401">Option</span></span> | <span data-ttu-id="5ec61-402">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-402">Default Value</span></span> | <span data-ttu-id="5ec61-403">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5ec61-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-404">32 KB</span></span> | <span data-ttu-id="5ec61-405">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="5ec61-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="5ec61-406">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5ec61-407">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="5ec61-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5ec61-408">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5ec61-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-409">32 KB</span></span> | <span data-ttu-id="5ec61-410">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="5ec61-411">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5ec61-412">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-412">All Transports are enabled.</span></span> | <span data-ttu-id="5ec61-413">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="5ec61-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5ec61-414">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-414">See below.</span></span> | <span data-ttu-id="5ec61-415">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5ec61-416">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-416">See below.</span></span> | <span data-ttu-id="5ec61-417">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="5ec61-418">0</span><span class="sxs-lookup"><span data-stu-id="5ec61-418">0</span></span> | <span data-ttu-id="5ec61-419">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="5ec61-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="5ec61-420">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="5ec61-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="5ec61-421">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5ec61-422">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-422">Option</span></span> | <span data-ttu-id="5ec61-423">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-423">Default Value</span></span> | <span data-ttu-id="5ec61-424">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5ec61-425">90 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-425">90 seconds</span></span> | <span data-ttu-id="5ec61-426">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5ec61-427">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5ec61-428">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5ec61-429">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-429">Option</span></span> | <span data-ttu-id="5ec61-430">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-430">Default Value</span></span> | <span data-ttu-id="5ec61-431">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5ec61-432">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-432">5 seconds</span></span> | <span data-ttu-id="5ec61-433">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5ec61-434">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5ec61-435">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5ec61-436">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="5ec61-436">Configure client options</span></span>

<span data-ttu-id="5ec61-437">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5ec61-438">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="5ec61-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5ec61-439">registro</span><span class="sxs-lookup"><span data-stu-id="5ec61-439">Configure logging</span></span>

<span data-ttu-id="5ec61-440">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5ec61-441">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5ec61-442">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="5ec61-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-443">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="5ec61-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5ec61-444">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5ec61-445">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5ec61-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5ec61-446">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5ec61-447">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="5ec61-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5ec61-448">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="5ec61-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5ec61-449">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="5ec61-450">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="5ec61-451">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="5ec61-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="5ec61-452">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-452">The following table lists the available log levels.</span></span> <span data-ttu-id="5ec61-453">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="5ec61-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="5ec61-454">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="5ec61-455">String</span><span class="sxs-lookup"><span data-stu-id="5ec61-455">String</span></span>                      | <span data-ttu-id="5ec61-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="5ec61-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="5ec61-457">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="5ec61-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="5ec61-458">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="5ec61-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="5ec61-459">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5ec61-460">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="5ec61-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5ec61-461">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5ec61-462">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="5ec61-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5ec61-463">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="5ec61-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5ec61-464">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="5ec61-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5ec61-465">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5ec61-466">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="5ec61-466">Configure allowed transports</span></span>

<span data-ttu-id="5ec61-467">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5ec61-468">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5ec61-469">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ec61-469">All transports are enabled by default.</span></span>

<span data-ttu-id="5ec61-470">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="5ec61-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5ec61-471">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5ec61-472">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="5ec61-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="5ec61-473">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="5ec61-474">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5ec61-475">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="5ec61-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5ec61-476">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="5ec61-476">Configure bearer authentication</span></span>

<span data-ttu-id="5ec61-477">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5ec61-478">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="5ec61-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5ec61-479">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5ec61-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5ec61-480">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5ec61-481">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5ec61-482">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="5ec61-483">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5ec61-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5ec61-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="5ec61-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5ec61-485">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5ec61-486">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="5ec61-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5ec61-487">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-488">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-489">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-489">Option</span></span> | <span data-ttu-id="5ec61-490">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-490">Default value</span></span> | <span data-ttu-id="5ec61-491">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5ec61-492">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-493">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-493">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-494">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-495">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-496">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5ec61-497">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-497">15 seconds</span></span> | <span data-ttu-id="5ec61-498">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-499">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-500">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-501">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-502">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-502">15 seconds</span></span> | <span data-ttu-id="5ec61-503">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-504">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-505">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5ec61-506">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="5ec61-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5ec61-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-508">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-508">Option</span></span> | <span data-ttu-id="5ec61-509">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-509">Default value</span></span> | <span data-ttu-id="5ec61-510">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5ec61-511">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-512">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-512">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-513">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5ec61-514">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-515">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5ec61-516">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-517">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-518">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-519">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-520">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-520">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-521">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-521">Option</span></span> | <span data-ttu-id="5ec61-522">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-522">Default value</span></span> | <span data-ttu-id="5ec61-523">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5ec61-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5ec61-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5ec61-525">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-526">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-526">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-527">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-528">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-529">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5ec61-530">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-530">15 seconds</span></span> | <span data-ttu-id="5ec61-531">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-532">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-533">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-534">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5ec61-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5ec61-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5ec61-536">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-537">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-538">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-539">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5ec61-540">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-540">Configure additional options</span></span>

<span data-ttu-id="5ec61-541">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="5ec61-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-542">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-543">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-543">.NET Option</span></span> |  <span data-ttu-id="5ec61-544">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-544">Default value</span></span> | <span data-ttu-id="5ec61-545">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5ec61-546">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5ec61-547">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-548">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-549">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5ec61-550">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-550">Empty</span></span> | <span data-ttu-id="5ec61-551">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5ec61-552">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-552">Empty</span></span> | <span data-ttu-id="5ec61-553">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5ec61-554">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-554">Empty</span></span> | <span data-ttu-id="5ec61-555">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5ec61-556">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-556">5 seconds</span></span> | <span data-ttu-id="5ec61-557">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-557">WebSockets only.</span></span> <span data-ttu-id="5ec61-558">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="5ec61-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5ec61-559">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="5ec61-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5ec61-560">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-560">Empty</span></span> | <span data-ttu-id="5ec61-561">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5ec61-562">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5ec61-563">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="5ec61-564">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5ec61-565">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5ec61-566">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="5ec61-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5ec61-567">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5ec61-568">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5ec61-569">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="5ec61-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5ec61-570">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5ec61-571">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="5ec61-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5ec61-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-573">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-573">JavaScript Option</span></span> | <span data-ttu-id="5ec61-574">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-574">Default Value</span></span> | <span data-ttu-id="5ec61-575">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5ec61-576">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5ec61-577">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-578">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-579">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-580">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-580">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-581">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-581">Java Option</span></span> | <span data-ttu-id="5ec61-582">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-582">Default Value</span></span> | <span data-ttu-id="5ec61-583">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5ec61-584">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5ec61-585">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-586">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-587">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5ec61-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5ec61-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5ec61-589">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-589">Empty</span></span> | <span data-ttu-id="5ec61-590">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5ec61-591">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5ec61-592">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5ec61-593">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5ec61-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5ec61-594">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5ec61-595">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-596">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="5ec61-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5ec61-597">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="5ec61-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5ec61-598">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="5ec61-599">`AddJsonProtocol`se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5ec61-600">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="5ec61-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5ec61-601">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5ec61-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5ec61-602">Para obtener más información, consulte la [documentación deSystem.Text.Js](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="5ec61-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="5ec61-603">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase" predeterminados, use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="5ec61-604">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ec61-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5ec61-605">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5ec61-606">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="5ec61-607">Cambiar a Newtonsoft.Jsactivado</span><span class="sxs-lookup"><span data-stu-id="5ec61-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="5ec61-608">Si necesita características de `Newtonsoft.Json` que no se admiten en `System.Text.Json` , consulte [cambiar a Newtonsoft.Jsactivado](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="5ec61-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5ec61-609">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-609">MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-610">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5ec61-611">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-612">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5ec61-613">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="5ec61-613">Configure server options</span></span>

<span data-ttu-id="5ec61-614">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="5ec61-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5ec61-615">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-615">Option</span></span> | <span data-ttu-id="5ec61-616">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-616">Default Value</span></span> | <span data-ttu-id="5ec61-617">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5ec61-618">30 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-618">30 seconds</span></span> | <span data-ttu-id="5ec61-619">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5ec61-620">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5ec61-621">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5ec61-622">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-622">15 seconds</span></span> | <span data-ttu-id="5ec61-623">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="5ec61-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5ec61-624">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-625">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-626">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-626">15 seconds</span></span> | <span data-ttu-id="5ec61-627">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5ec61-628">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5ec61-629">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5ec61-630">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="5ec61-630">All installed protocols</span></span> | <span data-ttu-id="5ec61-631">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-631">Protocols supported by this hub.</span></span> <span data-ttu-id="5ec61-632">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="5ec61-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5ec61-633">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5ec61-634">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="5ec61-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="5ec61-635">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="5ec61-636">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="5ec61-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-637">32 KB</span></span> | <span data-ttu-id="5ec61-638">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="5ec61-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="5ec61-639">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5ec61-640">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="5ec61-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5ec61-641">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="5ec61-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5ec61-642">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5ec61-643">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="5ec61-644">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="5ec61-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5ec61-645">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-645">Option</span></span> | <span data-ttu-id="5ec61-646">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-646">Default Value</span></span> | <span data-ttu-id="5ec61-647">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5ec61-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-648">32 KB</span></span> | <span data-ttu-id="5ec61-649">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="5ec61-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="5ec61-650">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5ec61-651">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="5ec61-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5ec61-652">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5ec61-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-653">32 KB</span></span> | <span data-ttu-id="5ec61-654">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="5ec61-655">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5ec61-656">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-656">All Transports are enabled.</span></span> | <span data-ttu-id="5ec61-657">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="5ec61-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5ec61-658">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-658">See below.</span></span> | <span data-ttu-id="5ec61-659">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5ec61-660">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-660">See below.</span></span> | <span data-ttu-id="5ec61-661">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5ec61-662">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5ec61-663">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-663">Option</span></span> | <span data-ttu-id="5ec61-664">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-664">Default Value</span></span> | <span data-ttu-id="5ec61-665">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5ec61-666">90 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-666">90 seconds</span></span> | <span data-ttu-id="5ec61-667">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5ec61-668">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5ec61-669">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5ec61-670">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-670">Option</span></span> | <span data-ttu-id="5ec61-671">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-671">Default Value</span></span> | <span data-ttu-id="5ec61-672">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5ec61-673">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-673">5 seconds</span></span> | <span data-ttu-id="5ec61-674">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5ec61-675">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5ec61-676">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5ec61-677">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="5ec61-677">Configure client options</span></span>

<span data-ttu-id="5ec61-678">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5ec61-679">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="5ec61-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5ec61-680">registro</span><span class="sxs-lookup"><span data-stu-id="5ec61-680">Configure logging</span></span>

<span data-ttu-id="5ec61-681">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5ec61-682">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5ec61-683">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="5ec61-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-684">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="5ec61-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5ec61-685">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5ec61-686">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5ec61-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5ec61-687">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5ec61-688">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="5ec61-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5ec61-689">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="5ec61-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5ec61-690">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="5ec61-691">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="5ec61-692">Esto resulta útil cuando se configura SignalR el inicio de sesión en entornos donde no se tiene acceso a las `LogLevel` constantes.</span><span class="sxs-lookup"><span data-stu-id="5ec61-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="5ec61-693">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-693">The following table lists the available log levels.</span></span> <span data-ttu-id="5ec61-694">El valor que se proporciona para `configureLogging` establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="5ec61-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="5ec61-695">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="5ec61-696">String</span><span class="sxs-lookup"><span data-stu-id="5ec61-696">String</span></span>                      | <span data-ttu-id="5ec61-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="5ec61-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="5ec61-698">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="5ec61-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="5ec61-699">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="5ec61-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="5ec61-700">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5ec61-701">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="5ec61-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5ec61-702">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5ec61-703">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="5ec61-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5ec61-704">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="5ec61-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5ec61-705">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="5ec61-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5ec61-706">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5ec61-707">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="5ec61-707">Configure allowed transports</span></span>

<span data-ttu-id="5ec61-708">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5ec61-709">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5ec61-710">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ec61-710">All transports are enabled by default.</span></span>

<span data-ttu-id="5ec61-711">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="5ec61-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5ec61-712">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5ec61-713">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="5ec61-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="5ec61-714">En el cliente de Java, el transporte se selecciona con el `withTransport` método en `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="5ec61-715">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5ec61-716">El SignalR cliente de Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="5ec61-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5ec61-717">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="5ec61-717">Configure bearer authentication</span></span>

<span data-ttu-id="5ec61-718">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5ec61-719">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="5ec61-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5ec61-720">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5ec61-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5ec61-721">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5ec61-722">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5ec61-723">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="5ec61-724">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5ec61-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5ec61-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="5ec61-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5ec61-726">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5ec61-727">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="5ec61-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5ec61-728">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-729">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-730">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-730">Option</span></span> | <span data-ttu-id="5ec61-731">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-731">Default value</span></span> | <span data-ttu-id="5ec61-732">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5ec61-733">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-734">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-734">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-735">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-736">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-737">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5ec61-738">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-738">15 seconds</span></span> | <span data-ttu-id="5ec61-739">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-740">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-741">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-742">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-743">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-743">15 seconds</span></span> | <span data-ttu-id="5ec61-744">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-745">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-746">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5ec61-747">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="5ec61-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5ec61-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-749">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-749">Option</span></span> | <span data-ttu-id="5ec61-750">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-750">Default value</span></span> | <span data-ttu-id="5ec61-751">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5ec61-752">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-753">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-753">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-754">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5ec61-755">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-756">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5ec61-757">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-758">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-759">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-760">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-761">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-761">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-762">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-762">Option</span></span> | <span data-ttu-id="5ec61-763">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-763">Default value</span></span> | <span data-ttu-id="5ec61-764">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5ec61-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5ec61-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5ec61-766">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-767">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-767">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-768">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-769">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-770">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5ec61-771">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-771">15 seconds</span></span> | <span data-ttu-id="5ec61-772">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-773">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-774">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-775">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5ec61-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5ec61-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5ec61-777">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-778">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-779">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-780">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5ec61-781">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-781">Configure additional options</span></span>

<span data-ttu-id="5ec61-782">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="5ec61-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-783">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-784">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-784">.NET Option</span></span> |  <span data-ttu-id="5ec61-785">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-785">Default value</span></span> | <span data-ttu-id="5ec61-786">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5ec61-787">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5ec61-788">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-789">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-790">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5ec61-791">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-791">Empty</span></span> | <span data-ttu-id="5ec61-792">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5ec61-793">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-793">Empty</span></span> | <span data-ttu-id="5ec61-794">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5ec61-795">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-795">Empty</span></span> | <span data-ttu-id="5ec61-796">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5ec61-797">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-797">5 seconds</span></span> | <span data-ttu-id="5ec61-798">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-798">WebSockets only.</span></span> <span data-ttu-id="5ec61-799">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="5ec61-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5ec61-800">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="5ec61-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5ec61-801">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-801">Empty</span></span> | <span data-ttu-id="5ec61-802">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5ec61-803">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5ec61-804">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="5ec61-805">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5ec61-806">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5ec61-807">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="5ec61-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5ec61-808">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5ec61-809">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5ec61-810">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="5ec61-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5ec61-811">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5ec61-812">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="5ec61-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5ec61-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-814">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-814">JavaScript Option</span></span> | <span data-ttu-id="5ec61-815">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-815">Default Value</span></span> | <span data-ttu-id="5ec61-816">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5ec61-817">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5ec61-818">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-819">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-820">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-821">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-821">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-822">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-822">Java Option</span></span> | <span data-ttu-id="5ec61-823">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-823">Default Value</span></span> | <span data-ttu-id="5ec61-824">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5ec61-825">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5ec61-826">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-827">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-828">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5ec61-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5ec61-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5ec61-830">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-830">Empty</span></span> | <span data-ttu-id="5ec61-831">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5ec61-832">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5ec61-833">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5ec61-834">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5ec61-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5ec61-835">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5ec61-836">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-837">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="5ec61-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5ec61-838">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="5ec61-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5ec61-839">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5ec61-840">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="5ec61-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5ec61-841">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5ec61-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5ec61-842">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="5ec61-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="5ec61-843">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="5ec61-844">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ec61-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5ec61-845">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5ec61-846">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5ec61-847">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-847">MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-848">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5ec61-849">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-850">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5ec61-851">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="5ec61-851">Configure server options</span></span>

<span data-ttu-id="5ec61-852">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="5ec61-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5ec61-853">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-853">Option</span></span> | <span data-ttu-id="5ec61-854">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-854">Default Value</span></span> | <span data-ttu-id="5ec61-855">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5ec61-856">30 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-856">30 seconds</span></span> | <span data-ttu-id="5ec61-857">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5ec61-858">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5ec61-859">El valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5ec61-860">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-860">15 seconds</span></span> | <span data-ttu-id="5ec61-861">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="5ec61-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5ec61-862">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-863">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-864">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-864">15 seconds</span></span> | <span data-ttu-id="5ec61-865">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5ec61-866">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5ec61-867">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5ec61-868">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="5ec61-868">All installed protocols</span></span> | <span data-ttu-id="5ec61-869">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-869">Protocols supported by this hub.</span></span> <span data-ttu-id="5ec61-870">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="5ec61-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5ec61-871">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5ec61-872">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="5ec61-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="5ec61-873">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5ec61-874">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="5ec61-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5ec61-875">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="5ec61-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5ec61-876">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5ec61-877">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="5ec61-878">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="5ec61-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5ec61-879">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-879">Option</span></span> | <span data-ttu-id="5ec61-880">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-880">Default Value</span></span> | <span data-ttu-id="5ec61-881">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5ec61-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-882">32 KB</span></span> | <span data-ttu-id="5ec61-883">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="5ec61-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="5ec61-884">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5ec61-885">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="5ec61-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5ec61-886">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5ec61-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-887">32 KB</span></span> | <span data-ttu-id="5ec61-888">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="5ec61-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="5ec61-889">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5ec61-890">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-890">All Transports are enabled.</span></span> | <span data-ttu-id="5ec61-891">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="5ec61-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5ec61-892">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-892">See below.</span></span> | <span data-ttu-id="5ec61-893">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5ec61-894">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-894">See below.</span></span> | <span data-ttu-id="5ec61-895">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5ec61-896">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5ec61-897">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-897">Option</span></span> | <span data-ttu-id="5ec61-898">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-898">Default Value</span></span> | <span data-ttu-id="5ec61-899">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5ec61-900">90 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-900">90 seconds</span></span> | <span data-ttu-id="5ec61-901">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5ec61-902">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5ec61-903">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5ec61-904">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-904">Option</span></span> | <span data-ttu-id="5ec61-905">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-905">Default Value</span></span> | <span data-ttu-id="5ec61-906">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5ec61-907">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-907">5 seconds</span></span> | <span data-ttu-id="5ec61-908">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5ec61-909">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5ec61-910">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5ec61-911">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="5ec61-911">Configure client options</span></span>

<span data-ttu-id="5ec61-912">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5ec61-913">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="5ec61-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5ec61-914">registro</span><span class="sxs-lookup"><span data-stu-id="5ec61-914">Configure logging</span></span>

<span data-ttu-id="5ec61-915">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5ec61-916">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5ec61-917">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="5ec61-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-918">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="5ec61-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5ec61-919">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5ec61-920">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5ec61-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5ec61-921">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5ec61-922">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="5ec61-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5ec61-923">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="5ec61-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5ec61-924">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5ec61-925">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5ec61-926">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="5ec61-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5ec61-927">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5ec61-928">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="5ec61-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5ec61-929">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="5ec61-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5ec61-930">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="5ec61-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5ec61-931">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5ec61-932">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="5ec61-932">Configure allowed transports</span></span>

<span data-ttu-id="5ec61-933">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5ec61-934">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5ec61-935">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ec61-935">All transports are enabled by default.</span></span>

<span data-ttu-id="5ec61-936">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="5ec61-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5ec61-937">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="5ec61-938">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="5ec61-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5ec61-939">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="5ec61-939">Configure bearer authentication</span></span>

<span data-ttu-id="5ec61-940">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5ec61-941">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="5ec61-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5ec61-942">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5ec61-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5ec61-943">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5ec61-944">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5ec61-945">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="5ec61-946">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5ec61-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5ec61-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="5ec61-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5ec61-948">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5ec61-949">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="5ec61-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5ec61-950">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-951">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-952">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-952">Option</span></span> | <span data-ttu-id="5ec61-953">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-953">Default value</span></span> | <span data-ttu-id="5ec61-954">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5ec61-955">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-956">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-956">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-957">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-958">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-959">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5ec61-960">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-960">15 seconds</span></span> | <span data-ttu-id="5ec61-961">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-962">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-963">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-964">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-965">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-965">15 seconds</span></span> | <span data-ttu-id="5ec61-966">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-967">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-968">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="5ec61-969">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="5ec61-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5ec61-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-971">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-971">Option</span></span> | <span data-ttu-id="5ec61-972">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-972">Default value</span></span> | <span data-ttu-id="5ec61-973">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5ec61-974">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-975">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-975">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-976">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5ec61-977">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-978">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="5ec61-979">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-980">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-981">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-982">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-983">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-983">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-984">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-984">Option</span></span> | <span data-ttu-id="5ec61-985">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-985">Default value</span></span> | <span data-ttu-id="5ec61-986">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5ec61-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5ec61-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5ec61-988">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-989">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-989">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-990">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-991">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-992">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5ec61-993">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-993">15 seconds</span></span> | <span data-ttu-id="5ec61-994">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-995">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-996">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-997">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="5ec61-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="5ec61-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="5ec61-999">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-1000">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="5ec61-1001">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="5ec61-1002">Si el cliente no ha enviado un mensaje en el `ClientTimeoutInterval` conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5ec61-1003">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-1003">Configure additional options</span></span>

<span data-ttu-id="5ec61-1004">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-1006">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-1006">.NET Option</span></span> |  <span data-ttu-id="5ec61-1007">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1007">Default value</span></span> | <span data-ttu-id="5ec61-1008">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5ec61-1009">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5ec61-1010">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-1011">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-1012">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5ec61-1013">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1013">Empty</span></span> | <span data-ttu-id="5ec61-1014">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5ec61-1015">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1015">Empty</span></span> | <span data-ttu-id="5ec61-1016">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5ec61-1017">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1017">Empty</span></span> | <span data-ttu-id="5ec61-1018">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5ec61-1019">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1019">5 seconds</span></span> | <span data-ttu-id="5ec61-1020">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1020">WebSockets only.</span></span> <span data-ttu-id="5ec61-1021">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5ec61-1022">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5ec61-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1023">Empty</span></span> | <span data-ttu-id="5ec61-1024">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5ec61-1025">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5ec61-1026">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="5ec61-1027">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5ec61-1028">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5ec61-1029">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="5ec61-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5ec61-1030">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5ec61-1031">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5ec61-1032">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5ec61-1033">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5ec61-1034">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5ec61-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-1036">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-1036">JavaScript Option</span></span> | <span data-ttu-id="5ec61-1037">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1037">Default Value</span></span> | <span data-ttu-id="5ec61-1038">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5ec61-1039">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5ec61-1040">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-1041">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-1042">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-1043">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-1044">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-1044">Java Option</span></span> | <span data-ttu-id="5ec61-1045">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1045">Default Value</span></span> | <span data-ttu-id="5ec61-1046">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5ec61-1047">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5ec61-1048">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-1049">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-1050">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5ec61-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5ec61-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5ec61-1052">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1052">Empty</span></span> | <span data-ttu-id="5ec61-1053">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5ec61-1054">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5ec61-1055">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5ec61-1056">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5ec61-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5ec61-1057">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5ec61-1058">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-1059">ASP.NET Core SignalR admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5ec61-1060">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5ec61-1061">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5ec61-1062">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5ec61-1063">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un `JsonSerializerSettings` objeto JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5ec61-1064">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="5ec61-1065">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", use el código siguiente en `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="5ec61-1066">En el cliente de .NET, el mismo `AddJsonProtocol` método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5ec61-1067">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5ec61-1068">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5ec61-1069">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="5ec61-1069">MessagePack serialization options</span></span>

<span data-ttu-id="5ec61-1070">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5ec61-1071">Vea [MessagePack en SignalR ](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-1072">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5ec61-1073">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="5ec61-1073">Configure server options</span></span>

<span data-ttu-id="5ec61-1074">En la tabla siguiente se describen las opciones para configurar los SignalR concentradores:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="5ec61-1075">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1075">Option</span></span> | <span data-ttu-id="5ec61-1076">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1076">Default Value</span></span> | <span data-ttu-id="5ec61-1077">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="5ec61-1078">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1078">15 seconds</span></span> | <span data-ttu-id="5ec61-1079">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5ec61-1080">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-1081">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5ec61-1082">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1082">15 seconds</span></span> | <span data-ttu-id="5ec61-1083">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5ec61-1084">Al cambiar `KeepAliveInterval` , cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5ec61-1085">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado es el doble del `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5ec61-1086">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="5ec61-1086">All installed protocols</span></span> | <span data-ttu-id="5ec61-1087">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="5ec61-1088">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5ec61-1089">Si `true` es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5ec61-1090">El valor predeterminado es `false` , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="5ec61-1091">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de opciones a la `AddSignalR` llamada en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5ec61-1092">Las opciones de un solo concentrador invalidan las opciones globales proporcionadas en `AddSignalR` y se pueden configurar mediante <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5ec61-1093">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="5ec61-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5ec61-1094">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5ec61-1095">Estas opciones se configuran pasando un delegado [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="5ec61-1096">En la tabla siguiente se describen las opciones para configurar SignalR las opciones http avanzadas de ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5ec61-1097">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1097">Option</span></span> | <span data-ttu-id="5ec61-1098">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1098">Default Value</span></span> | <span data-ttu-id="5ec61-1099">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5ec61-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-1100">32 KB</span></span> | <span data-ttu-id="5ec61-1101">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="5ec61-1102">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5ec61-1103">Los datos se recopilan automáticamente a partir de los `Authorize` atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5ec61-1104">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5ec61-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="5ec61-1105">32 KB</span></span> | <span data-ttu-id="5ec61-1106">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="5ec61-1107">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5ec61-1108">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1108">All Transports are enabled.</span></span> | <span data-ttu-id="5ec61-1109">Una enumeración de marcas de bits `HttpTransportType` que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5ec61-1110">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1110">See below.</span></span> | <span data-ttu-id="5ec61-1111">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5ec61-1112">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1112">See below.</span></span> | <span data-ttu-id="5ec61-1113">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5ec61-1114">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante la `LongPolling` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5ec61-1115">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1115">Option</span></span> | <span data-ttu-id="5ec61-1116">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1116">Default Value</span></span> | <span data-ttu-id="5ec61-1117">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5ec61-1118">90 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1118">90 seconds</span></span> | <span data-ttu-id="5ec61-1119">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5ec61-1120">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5ec61-1121">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante la `WebSockets` propiedad:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5ec61-1122">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1122">Option</span></span> | <span data-ttu-id="5ec61-1123">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1123">Default Value</span></span> | <span data-ttu-id="5ec61-1124">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5ec61-1125">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1125">5 seconds</span></span> | <span data-ttu-id="5ec61-1126">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5ec61-1127">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5ec61-1128">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5ec61-1129">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="5ec61-1129">Configure client options</span></span>

<span data-ttu-id="5ec61-1130">Las opciones de cliente se pueden configurar en el `HubConnectionBuilder` tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5ec61-1131">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como en el `HubConnection` propio.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5ec61-1132">registro</span><span class="sxs-lookup"><span data-stu-id="5ec61-1132">Configure logging</span></span>

<span data-ttu-id="5ec61-1133">El registro se configura en el cliente .NET mediante el `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5ec61-1134">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5ec61-1135">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5ec61-1136">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5ec61-1137">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5ec61-1138">Por ejemplo, para habilitar el registro de la consola, instale el `Microsoft.Extensions.Logging.Console` paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5ec61-1139">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5ec61-1140">En el cliente de JavaScript, `configureLogging` existe un método similar.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5ec61-1141">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5ec61-1142">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5ec61-1143">Para deshabilitar el registro por completo, especifique `signalR.LogLevel.None` en el `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5ec61-1144">Para obtener más información sobre el registro, consulte la [ SignalR documentación de diagnóstico](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5ec61-1145">El SignalR cliente de Java utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5ec61-1146">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5ec61-1147">En el fragmento de código siguiente se muestra cómo utilizar `java.util.logging` con el SignalR cliente de Java.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5ec61-1148">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5ec61-1149">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5ec61-1150">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1150">Configure allowed transports</span></span>

<span data-ttu-id="5ec61-1151">Los transportes utilizados por SignalR pueden configurarse en la `WithUrl` llamada ( `withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5ec61-1152">Una operación OR bit a bit de los valores de `HttpTransportType` se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5ec61-1153">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="5ec61-1154">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5ec61-1155">En el cliente de JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto de opciones proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5ec61-1156">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="5ec61-1156">Configure bearer authentication</span></span>

<span data-ttu-id="5ec61-1157">Para proporcionar datos de autenticación junto con SignalR las solicitudes, use la `AccessTokenProvider` opción ( `accessTokenFactory` en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5ec61-1158">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP (mediante el `Authorization` encabezado con un tipo de `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5ec61-1159">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5ec61-1160">En estos casos, el token de acceso se proporciona como un valor de cadena de consulta `access_token` .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5ec61-1161">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de opciones en `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5ec61-1162">En el cliente de JavaScript, el token de acceso se configura estableciendo el `accessTokenFactory` campo en el objeto de opciones en `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="5ec61-1163">En el SignalR cliente de Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5ec61-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [única \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5ec61-1165">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5ec61-1166">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="5ec61-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5ec61-1167">En el propio objeto están disponibles opciones adicionales para configurar el comportamiento de tiempo de espera y mantenimiento de conexión `HubConnection` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-1169">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1169">Option</span></span> | <span data-ttu-id="5ec61-1170">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1170">Default value</span></span> | <span data-ttu-id="5ec61-1171">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5ec61-1172">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-1173">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1173">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-1174">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-1175">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-1176">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5ec61-1177">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1177">15 seconds</span></span> | <span data-ttu-id="5ec61-1178">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-1179">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `Closed` evento ( `onclose` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5ec61-1180">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-1181">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="5ec61-1182">En el cliente de .NET, los valores de tiempo de espera se especifican como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="5ec61-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-1184">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1184">Option</span></span> | <span data-ttu-id="5ec61-1185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1185">Default value</span></span> | <span data-ttu-id="5ec61-1186">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5ec61-1187">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-1188">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1188">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-1189">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5ec61-1190">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-1191">El valor recomendado es un número, como mínimo, el doble del valor del servidor `KeepAliveInterval` para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-1192">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-1193">Opción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1193">Option</span></span> | <span data-ttu-id="5ec61-1194">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1194">Default value</span></span> | <span data-ttu-id="5ec61-1195">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="5ec61-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5ec61-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="5ec61-1197">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="5ec61-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5ec61-1198">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1198">Timeout for server activity.</span></span> <span data-ttu-id="5ec61-1199">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-1200">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5ec61-1201">El valor recomendado es un número al menos el doble del valor del servidor `KeepAliveInterval` , para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5ec61-1202">15 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1202">15 seconds</span></span> | <span data-ttu-id="5ec61-1203">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="5ec61-1204">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena el `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5ec61-1205">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5ec61-1206">Para obtener más información sobre el proceso de enlace, consulte la [ SignalR especificación del Protocolo de concentrador](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5ec61-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5ec61-1207">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-1207">Configure additional options</span></span>

<span data-ttu-id="5ec61-1208">Se pueden configurar opciones adicionales en el `WithUrl` `withUrl` método (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="5ec61-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="5ec61-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5ec61-1210">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="5ec61-1210">.NET Option</span></span> |  <span data-ttu-id="5ec61-1211">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1211">Default value</span></span> | <span data-ttu-id="5ec61-1212">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5ec61-1213">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5ec61-1214">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-1215">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-1216">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5ec61-1217">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1217">Empty</span></span> | <span data-ttu-id="5ec61-1218">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5ec61-1219">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1219">Empty</span></span> | <span data-ttu-id="5ec61-1220">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5ec61-1221">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1221">Empty</span></span> | <span data-ttu-id="5ec61-1222">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5ec61-1223">5 segundos</span><span class="sxs-lookup"><span data-stu-id="5ec61-1223">5 seconds</span></span> | <span data-ttu-id="5ec61-1224">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1224">WebSockets only.</span></span> <span data-ttu-id="5ec61-1225">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5ec61-1226">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5ec61-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1227">Empty</span></span> | <span data-ttu-id="5ec61-1228">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5ec61-1229">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5ec61-1230">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="5ec61-1231">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5ec61-1232">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5ec61-1233">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="5ec61-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5ec61-1234">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5ec61-1235">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5ec61-1236">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5ec61-1237">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5ec61-1238">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="5ec61-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5ec61-1240">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="5ec61-1240">JavaScript Option</span></span> | <span data-ttu-id="5ec61-1241">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1241">Default Value</span></span> | <span data-ttu-id="5ec61-1242">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5ec61-1243">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5ec61-1244">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-1245">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-1246">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="5ec61-1247">Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="5ec61-1248">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="5ec61-1248">Java Option</span></span> | <span data-ttu-id="5ec61-1249">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="5ec61-1249">Default Value</span></span> | <span data-ttu-id="5ec61-1250">Descripción</span><span class="sxs-lookup"><span data-stu-id="5ec61-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5ec61-1251">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5ec61-1252">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5ec61-1253">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5ec61-1254">Esta configuración no se puede habilitar cuando se usa el servicio de Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="5ec61-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5ec61-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5ec61-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5ec61-1256">Empty</span><span class="sxs-lookup"><span data-stu-id="5ec61-1256">Empty</span></span> | <span data-ttu-id="5ec61-1257">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="5ec61-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5ec61-1258">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones proporcionado a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5ec61-1259">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript proporcionado a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="5ec61-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5ec61-1260">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5ec61-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5ec61-1261">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5ec61-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
