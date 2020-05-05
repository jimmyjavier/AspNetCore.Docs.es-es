---
title: Configuración SignalR de ASP.net Core
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
ms.openlocfilehash: 054462c37fffd1973cbbe4f76ae4a3be5a6c1778
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767309"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="37f20-103">Configuración de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="37f20-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="37f20-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="37f20-105">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="37f20-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="37f20-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="37f20-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="37f20-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="37f20-108">`AddJsonProtocol`se puede agregar después [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de AddSignalR `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="37f20-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37f20-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="37f20-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="37f20-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="37f20-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="37f20-111">Para obtener más información, vea la [documentación de System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="37f20-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="37f20-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase `Startup.ConfigureServices`" predeterminados, use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="37f20-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="37f20-113">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="37f20-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="37f20-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="37f20-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="37f20-116">Cambiar a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="37f20-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="37f20-117">Si necesita características de `Newtonsoft.Json` que no se admiten `System.Text.Json`en, consulte [cambiar a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="37f20-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="37f20-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-118">MessagePack serialization options</span></span>

<span data-ttu-id="37f20-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="37f20-120">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="37f20-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="37f20-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="37f20-122">Configure server options</span></span>

<span data-ttu-id="37f20-123">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="37f20-124">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-124">Option</span></span> | <span data-ttu-id="37f20-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-125">Default Value</span></span> | <span data-ttu-id="37f20-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="37f20-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-127">30 seconds</span></span> | <span data-ttu-id="37f20-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="37f20-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="37f20-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="37f20-130">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="37f20-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-131">15 seconds</span></span> | <span data-ttu-id="37f20-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="37f20-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="37f20-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-134">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-135">15 seconds</span></span> | <span data-ttu-id="37f20-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="37f20-137">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="37f20-138">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="37f20-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="37f20-139">All installed protocols</span></span> | <span data-ttu-id="37f20-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-140">Protocols supported by this hub.</span></span> <span data-ttu-id="37f20-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="37f20-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="37f20-142">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="37f20-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="37f20-143">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="37f20-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="37f20-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="37f20-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="37f20-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-146">32 KB</span></span> | <span data-ttu-id="37f20-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="37f20-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="37f20-148">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="37f20-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="37f20-149">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="37f20-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="37f20-150">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="37f20-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="37f20-151">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="37f20-152">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="37f20-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="37f20-153">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="37f20-154">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-154">Option</span></span> | <span data-ttu-id="37f20-155">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-155">Default Value</span></span> | <span data-ttu-id="37f20-156">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="37f20-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-157">32 KB</span></span> | <span data-ttu-id="37f20-158">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="37f20-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="37f20-159">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="37f20-160">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="37f20-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="37f20-161">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="37f20-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-162">32 KB</span></span> | <span data-ttu-id="37f20-163">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="37f20-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="37f20-164">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="37f20-165">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="37f20-165">All Transports are enabled.</span></span> | <span data-ttu-id="37f20-166">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="37f20-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="37f20-167">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-167">See below.</span></span> | <span data-ttu-id="37f20-168">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="37f20-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="37f20-169">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-169">See below.</span></span> | <span data-ttu-id="37f20-170">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="37f20-171">0</span><span class="sxs-lookup"><span data-stu-id="37f20-171">0</span></span> | <span data-ttu-id="37f20-172">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="37f20-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="37f20-173">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="37f20-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="37f20-174">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="37f20-175">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-175">Option</span></span> | <span data-ttu-id="37f20-176">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-176">Default Value</span></span> | <span data-ttu-id="37f20-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="37f20-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-178">90 seconds</span></span> | <span data-ttu-id="37f20-179">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="37f20-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="37f20-180">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="37f20-181">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="37f20-182">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-182">Option</span></span> | <span data-ttu-id="37f20-183">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-183">Default Value</span></span> | <span data-ttu-id="37f20-184">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="37f20-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-185">5 seconds</span></span> | <span data-ttu-id="37f20-186">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="37f20-187">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="37f20-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="37f20-188">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="37f20-189">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="37f20-189">Configure client options</span></span>

<span data-ttu-id="37f20-190">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="37f20-191">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="37f20-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="37f20-192">registro</span><span class="sxs-lookup"><span data-stu-id="37f20-192">Configure logging</span></span>

<span data-ttu-id="37f20-193">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="37f20-194">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="37f20-195">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="37f20-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-196">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="37f20-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="37f20-197">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="37f20-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="37f20-198">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="37f20-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="37f20-199">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="37f20-200">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="37f20-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="37f20-201">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="37f20-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="37f20-202">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="37f20-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="37f20-203">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="37f20-204">Esto resulta útil cuando se configura el registro de Signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="37f20-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="37f20-205">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="37f20-205">The following table lists the available log levels.</span></span> <span data-ttu-id="37f20-206">El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="37f20-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="37f20-207">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="37f20-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="37f20-208">String</span><span class="sxs-lookup"><span data-stu-id="37f20-208">String</span></span>                      | <span data-ttu-id="37f20-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="37f20-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="37f20-210">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="37f20-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="37f20-211">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="37f20-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="37f20-212">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="37f20-213">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="37f20-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="37f20-214">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="37f20-215">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="37f20-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="37f20-216">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="37f20-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="37f20-217">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="37f20-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="37f20-218">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="37f20-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="37f20-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="37f20-219">Configure allowed transports</span></span>

<span data-ttu-id="37f20-220">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="37f20-221">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="37f20-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="37f20-222">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="37f20-222">All transports are enabled by default.</span></span>

<span data-ttu-id="37f20-223">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="37f20-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="37f20-224">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="37f20-225">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="37f20-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="37f20-226">En el cliente de Java, el transporte se selecciona con `withTransport` el método en `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="37f20-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="37f20-227">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="37f20-228">El cliente de Signalr Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="37f20-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="37f20-229">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="37f20-229">Configure bearer authentication</span></span>

<span data-ttu-id="37f20-230">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="37f20-231">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="37f20-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="37f20-232">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="37f20-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="37f20-233">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="37f20-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="37f20-234">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="37f20-235">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="37f20-236">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="37f20-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="37f20-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="37f20-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="37f20-238">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="37f20-239">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="37f20-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="37f20-240">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="37f20-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-241">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-242">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-242">Option</span></span> | <span data-ttu-id="37f20-243">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-243">Default value</span></span> | <span data-ttu-id="37f20-244">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="37f20-245">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-246">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-246">Timeout for server activity.</span></span> <span data-ttu-id="37f20-247">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-248">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-249">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="37f20-250">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-250">15 seconds</span></span> | <span data-ttu-id="37f20-251">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-252">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-253">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-254">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-255">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-255">15 seconds</span></span> | <span data-ttu-id="37f20-256">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-257">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-258">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="37f20-259">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="37f20-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="37f20-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-261">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-261">Option</span></span> | <span data-ttu-id="37f20-262">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-262">Default value</span></span> | <span data-ttu-id="37f20-263">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="37f20-264">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-265">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-265">Timeout for server activity.</span></span> <span data-ttu-id="37f20-266">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="37f20-267">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-268">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="37f20-269">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-270">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-271">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-272">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-273">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-273">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-274">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-274">Option</span></span> | <span data-ttu-id="37f20-275">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-275">Default value</span></span> | <span data-ttu-id="37f20-276">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="37f20-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="37f20-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="37f20-278">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-279">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-279">Timeout for server activity.</span></span> <span data-ttu-id="37f20-280">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-281">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-282">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="37f20-283">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-283">15 seconds</span></span> | <span data-ttu-id="37f20-284">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-285">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-286">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-287">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="37f20-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="37f20-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="37f20-289">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-290">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-291">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-292">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="37f20-293">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-293">Configure additional options</span></span>

<span data-ttu-id="37f20-294">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="37f20-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-295">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-296">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="37f20-296">.NET Option</span></span> |  <span data-ttu-id="37f20-297">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-297">Default value</span></span> | <span data-ttu-id="37f20-298">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="37f20-299">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="37f20-300">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-301">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-302">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="37f20-303">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-303">Empty</span></span> | <span data-ttu-id="37f20-304">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="37f20-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="37f20-305">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-305">Empty</span></span> | <span data-ttu-id="37f20-306">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="37f20-307">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-307">Empty</span></span> | <span data-ttu-id="37f20-308">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="37f20-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-309">5 seconds</span></span> | <span data-ttu-id="37f20-310">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-310">WebSockets only.</span></span> <span data-ttu-id="37f20-311">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="37f20-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="37f20-312">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="37f20-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="37f20-313">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-313">Empty</span></span> | <span data-ttu-id="37f20-314">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="37f20-315">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="37f20-316">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="37f20-317">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="37f20-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="37f20-318">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="37f20-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="37f20-319">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="37f20-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="37f20-320">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="37f20-321">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="37f20-322">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="37f20-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="37f20-323">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="37f20-324">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="37f20-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="37f20-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-326">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-326">JavaScript Option</span></span> | <span data-ttu-id="37f20-327">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-327">Default Value</span></span> | <span data-ttu-id="37f20-328">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="37f20-329">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="37f20-330">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-331">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-332">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="37f20-333">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="37f20-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="37f20-334">Azure App Service usa cookies para sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="37f20-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="37f20-335">Para obtener más información sobre CORS con Signalr, <xref:signalr/security#cross-origin-resource-sharing>consulte.</span><span class="sxs-lookup"><span data-stu-id="37f20-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-336">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-336">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-337">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="37f20-337">Java Option</span></span> | <span data-ttu-id="37f20-338">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-338">Default Value</span></span> | <span data-ttu-id="37f20-339">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="37f20-340">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="37f20-341">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-342">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-343">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="37f20-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="37f20-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="37f20-345">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-345">Empty</span></span> | <span data-ttu-id="37f20-346">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="37f20-347">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="37f20-348">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="37f20-349">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="37f20-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="37f20-350">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="37f20-351">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="37f20-352">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="37f20-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="37f20-353">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="37f20-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="37f20-354">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="37f20-355">`AddJsonProtocol`se puede agregar después [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de AddSignalR `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="37f20-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37f20-356">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="37f20-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="37f20-357">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="37f20-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="37f20-358">Para obtener más información, vea la [documentación de System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="37f20-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="37f20-359">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase `Startup.ConfigureServices`" predeterminados, use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="37f20-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="37f20-360">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="37f20-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="37f20-361">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="37f20-362">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="37f20-363">Cambiar a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="37f20-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="37f20-364">Si necesita características de `Newtonsoft.Json` que no se admiten `System.Text.Json`en, consulte [cambiar a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="37f20-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="37f20-365">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-365">MessagePack serialization options</span></span>

<span data-ttu-id="37f20-366">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="37f20-367">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="37f20-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-368">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="37f20-369">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="37f20-369">Configure server options</span></span>

<span data-ttu-id="37f20-370">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="37f20-371">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-371">Option</span></span> | <span data-ttu-id="37f20-372">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-372">Default Value</span></span> | <span data-ttu-id="37f20-373">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="37f20-374">30 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-374">30 seconds</span></span> | <span data-ttu-id="37f20-375">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="37f20-376">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="37f20-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="37f20-377">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="37f20-378">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-378">15 seconds</span></span> | <span data-ttu-id="37f20-379">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="37f20-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="37f20-380">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-381">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-382">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-382">15 seconds</span></span> | <span data-ttu-id="37f20-383">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="37f20-384">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="37f20-385">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="37f20-386">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="37f20-386">All installed protocols</span></span> | <span data-ttu-id="37f20-387">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-387">Protocols supported by this hub.</span></span> <span data-ttu-id="37f20-388">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="37f20-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="37f20-389">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="37f20-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="37f20-390">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="37f20-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="37f20-391">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="37f20-392">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="37f20-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-393">32 KB</span></span> | <span data-ttu-id="37f20-394">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="37f20-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="37f20-395">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="37f20-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="37f20-396">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="37f20-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="37f20-397">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="37f20-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="37f20-398">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="37f20-399">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="37f20-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="37f20-400">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="37f20-401">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-401">Option</span></span> | <span data-ttu-id="37f20-402">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-402">Default Value</span></span> | <span data-ttu-id="37f20-403">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="37f20-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-404">32 KB</span></span> | <span data-ttu-id="37f20-405">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="37f20-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="37f20-406">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="37f20-407">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="37f20-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="37f20-408">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="37f20-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-409">32 KB</span></span> | <span data-ttu-id="37f20-410">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="37f20-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="37f20-411">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="37f20-412">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="37f20-412">All Transports are enabled.</span></span> | <span data-ttu-id="37f20-413">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="37f20-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="37f20-414">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-414">See below.</span></span> | <span data-ttu-id="37f20-415">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="37f20-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="37f20-416">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-416">See below.</span></span> | <span data-ttu-id="37f20-417">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="37f20-418">0</span><span class="sxs-lookup"><span data-stu-id="37f20-418">0</span></span> | <span data-ttu-id="37f20-419">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="37f20-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="37f20-420">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="37f20-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="37f20-421">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="37f20-422">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-422">Option</span></span> | <span data-ttu-id="37f20-423">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-423">Default Value</span></span> | <span data-ttu-id="37f20-424">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="37f20-425">90 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-425">90 seconds</span></span> | <span data-ttu-id="37f20-426">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="37f20-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="37f20-427">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="37f20-428">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="37f20-429">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-429">Option</span></span> | <span data-ttu-id="37f20-430">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-430">Default Value</span></span> | <span data-ttu-id="37f20-431">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="37f20-432">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-432">5 seconds</span></span> | <span data-ttu-id="37f20-433">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="37f20-434">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="37f20-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="37f20-435">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="37f20-436">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="37f20-436">Configure client options</span></span>

<span data-ttu-id="37f20-437">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="37f20-438">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="37f20-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="37f20-439">registro</span><span class="sxs-lookup"><span data-stu-id="37f20-439">Configure logging</span></span>

<span data-ttu-id="37f20-440">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="37f20-441">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="37f20-442">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="37f20-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-443">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="37f20-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="37f20-444">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="37f20-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="37f20-445">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="37f20-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="37f20-446">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="37f20-447">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="37f20-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="37f20-448">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="37f20-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="37f20-449">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="37f20-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="37f20-450">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="37f20-451">Esto resulta útil cuando se configura el registro de Signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="37f20-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="37f20-452">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="37f20-452">The following table lists the available log levels.</span></span> <span data-ttu-id="37f20-453">El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="37f20-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="37f20-454">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="37f20-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="37f20-455">String</span><span class="sxs-lookup"><span data-stu-id="37f20-455">String</span></span>                      | <span data-ttu-id="37f20-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="37f20-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="37f20-457">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="37f20-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="37f20-458">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="37f20-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="37f20-459">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="37f20-460">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="37f20-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="37f20-461">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="37f20-462">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="37f20-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="37f20-463">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="37f20-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="37f20-464">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="37f20-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="37f20-465">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="37f20-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="37f20-466">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="37f20-466">Configure allowed transports</span></span>

<span data-ttu-id="37f20-467">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="37f20-468">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="37f20-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="37f20-469">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="37f20-469">All transports are enabled by default.</span></span>

<span data-ttu-id="37f20-470">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="37f20-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="37f20-471">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="37f20-472">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="37f20-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="37f20-473">En el cliente de Java, el transporte se selecciona con `withTransport` el método en `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="37f20-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="37f20-474">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="37f20-475">El cliente de Signalr Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="37f20-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="37f20-476">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="37f20-476">Configure bearer authentication</span></span>

<span data-ttu-id="37f20-477">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="37f20-478">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="37f20-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="37f20-479">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="37f20-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="37f20-480">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="37f20-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="37f20-481">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="37f20-482">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="37f20-483">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="37f20-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="37f20-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="37f20-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="37f20-485">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="37f20-486">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="37f20-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="37f20-487">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="37f20-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-488">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-489">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-489">Option</span></span> | <span data-ttu-id="37f20-490">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-490">Default value</span></span> | <span data-ttu-id="37f20-491">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="37f20-492">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-493">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-493">Timeout for server activity.</span></span> <span data-ttu-id="37f20-494">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-495">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-496">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="37f20-497">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-497">15 seconds</span></span> | <span data-ttu-id="37f20-498">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-499">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-500">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-501">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-502">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-502">15 seconds</span></span> | <span data-ttu-id="37f20-503">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-504">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-505">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="37f20-506">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="37f20-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="37f20-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-508">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-508">Option</span></span> | <span data-ttu-id="37f20-509">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-509">Default value</span></span> | <span data-ttu-id="37f20-510">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="37f20-511">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-512">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-512">Timeout for server activity.</span></span> <span data-ttu-id="37f20-513">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="37f20-514">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-515">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="37f20-516">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-517">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-518">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-519">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-520">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-520">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-521">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-521">Option</span></span> | <span data-ttu-id="37f20-522">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-522">Default value</span></span> | <span data-ttu-id="37f20-523">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="37f20-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="37f20-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="37f20-525">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-526">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-526">Timeout for server activity.</span></span> <span data-ttu-id="37f20-527">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-528">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-529">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="37f20-530">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-530">15 seconds</span></span> | <span data-ttu-id="37f20-531">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-532">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-533">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-534">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="37f20-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="37f20-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="37f20-536">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-537">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-538">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-539">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="37f20-540">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-540">Configure additional options</span></span>

<span data-ttu-id="37f20-541">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="37f20-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-542">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-543">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="37f20-543">.NET Option</span></span> |  <span data-ttu-id="37f20-544">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-544">Default value</span></span> | <span data-ttu-id="37f20-545">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="37f20-546">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="37f20-547">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-548">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-549">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="37f20-550">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-550">Empty</span></span> | <span data-ttu-id="37f20-551">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="37f20-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="37f20-552">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-552">Empty</span></span> | <span data-ttu-id="37f20-553">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="37f20-554">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-554">Empty</span></span> | <span data-ttu-id="37f20-555">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="37f20-556">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-556">5 seconds</span></span> | <span data-ttu-id="37f20-557">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-557">WebSockets only.</span></span> <span data-ttu-id="37f20-558">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="37f20-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="37f20-559">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="37f20-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="37f20-560">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-560">Empty</span></span> | <span data-ttu-id="37f20-561">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="37f20-562">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="37f20-563">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="37f20-564">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="37f20-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="37f20-565">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="37f20-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="37f20-566">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="37f20-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="37f20-567">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="37f20-568">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="37f20-569">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="37f20-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="37f20-570">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="37f20-571">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="37f20-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="37f20-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-573">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-573">JavaScript Option</span></span> | <span data-ttu-id="37f20-574">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-574">Default Value</span></span> | <span data-ttu-id="37f20-575">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="37f20-576">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="37f20-577">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-578">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-579">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-580">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-580">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-581">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="37f20-581">Java Option</span></span> | <span data-ttu-id="37f20-582">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-582">Default Value</span></span> | <span data-ttu-id="37f20-583">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="37f20-584">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="37f20-585">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-586">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-587">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="37f20-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="37f20-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="37f20-589">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-589">Empty</span></span> | <span data-ttu-id="37f20-590">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="37f20-591">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="37f20-592">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="37f20-593">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="37f20-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="37f20-594">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="37f20-595">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="37f20-596">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="37f20-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="37f20-597">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="37f20-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="37f20-598">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="37f20-599">`AddJsonProtocol`se puede agregar después [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de AddSignalR `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="37f20-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37f20-600">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="37f20-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="37f20-601">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="37f20-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="37f20-602">Para obtener más información, vea la [documentación de System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="37f20-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="37f20-603">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase `Startup.ConfigureServices`" predeterminados, use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="37f20-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="37f20-604">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="37f20-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="37f20-605">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="37f20-606">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="37f20-607">Cambiar a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="37f20-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="37f20-608">Si necesita características de `Newtonsoft.Json` que no se admiten `System.Text.Json`en, consulte [cambiar a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="37f20-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="37f20-609">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-609">MessagePack serialization options</span></span>

<span data-ttu-id="37f20-610">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="37f20-611">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="37f20-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-612">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="37f20-613">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="37f20-613">Configure server options</span></span>

<span data-ttu-id="37f20-614">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="37f20-615">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-615">Option</span></span> | <span data-ttu-id="37f20-616">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-616">Default Value</span></span> | <span data-ttu-id="37f20-617">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="37f20-618">30 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-618">30 seconds</span></span> | <span data-ttu-id="37f20-619">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="37f20-620">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="37f20-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="37f20-621">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="37f20-622">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-622">15 seconds</span></span> | <span data-ttu-id="37f20-623">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="37f20-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="37f20-624">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-625">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-626">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-626">15 seconds</span></span> | <span data-ttu-id="37f20-627">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="37f20-628">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="37f20-629">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="37f20-630">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="37f20-630">All installed protocols</span></span> | <span data-ttu-id="37f20-631">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-631">Protocols supported by this hub.</span></span> <span data-ttu-id="37f20-632">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="37f20-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="37f20-633">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="37f20-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="37f20-634">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="37f20-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="37f20-635">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="37f20-636">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="37f20-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-637">32 KB</span></span> | <span data-ttu-id="37f20-638">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="37f20-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="37f20-639">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="37f20-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="37f20-640">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="37f20-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="37f20-641">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="37f20-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="37f20-642">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="37f20-643">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="37f20-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="37f20-644">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="37f20-645">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-645">Option</span></span> | <span data-ttu-id="37f20-646">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-646">Default Value</span></span> | <span data-ttu-id="37f20-647">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="37f20-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-648">32 KB</span></span> | <span data-ttu-id="37f20-649">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="37f20-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="37f20-650">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="37f20-651">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="37f20-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="37f20-652">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="37f20-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-653">32 KB</span></span> | <span data-ttu-id="37f20-654">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="37f20-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="37f20-655">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="37f20-656">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="37f20-656">All Transports are enabled.</span></span> | <span data-ttu-id="37f20-657">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="37f20-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="37f20-658">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-658">See below.</span></span> | <span data-ttu-id="37f20-659">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="37f20-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="37f20-660">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-660">See below.</span></span> | <span data-ttu-id="37f20-661">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="37f20-662">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="37f20-663">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-663">Option</span></span> | <span data-ttu-id="37f20-664">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-664">Default Value</span></span> | <span data-ttu-id="37f20-665">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="37f20-666">90 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-666">90 seconds</span></span> | <span data-ttu-id="37f20-667">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="37f20-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="37f20-668">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="37f20-669">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="37f20-670">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-670">Option</span></span> | <span data-ttu-id="37f20-671">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-671">Default Value</span></span> | <span data-ttu-id="37f20-672">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="37f20-673">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-673">5 seconds</span></span> | <span data-ttu-id="37f20-674">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="37f20-675">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="37f20-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="37f20-676">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="37f20-677">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="37f20-677">Configure client options</span></span>

<span data-ttu-id="37f20-678">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="37f20-679">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="37f20-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="37f20-680">registro</span><span class="sxs-lookup"><span data-stu-id="37f20-680">Configure logging</span></span>

<span data-ttu-id="37f20-681">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="37f20-682">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="37f20-683">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="37f20-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-684">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="37f20-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="37f20-685">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="37f20-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="37f20-686">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="37f20-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="37f20-687">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="37f20-688">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="37f20-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="37f20-689">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="37f20-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="37f20-690">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="37f20-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="37f20-691">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="37f20-692">Esto resulta útil cuando se configura el registro de Signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="37f20-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="37f20-693">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="37f20-693">The following table lists the available log levels.</span></span> <span data-ttu-id="37f20-694">El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="37f20-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="37f20-695">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="37f20-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="37f20-696">String</span><span class="sxs-lookup"><span data-stu-id="37f20-696">String</span></span>                      | <span data-ttu-id="37f20-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="37f20-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="37f20-698">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="37f20-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="37f20-699">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="37f20-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="37f20-700">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="37f20-701">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="37f20-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="37f20-702">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="37f20-703">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="37f20-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="37f20-704">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="37f20-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="37f20-705">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="37f20-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="37f20-706">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="37f20-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="37f20-707">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="37f20-707">Configure allowed transports</span></span>

<span data-ttu-id="37f20-708">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="37f20-709">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="37f20-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="37f20-710">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="37f20-710">All transports are enabled by default.</span></span>

<span data-ttu-id="37f20-711">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="37f20-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="37f20-712">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="37f20-713">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="37f20-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="37f20-714">En el cliente de Java, el transporte se selecciona con `withTransport` el método en `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="37f20-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="37f20-715">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="37f20-716">El cliente de Signalr Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="37f20-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="37f20-717">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="37f20-717">Configure bearer authentication</span></span>

<span data-ttu-id="37f20-718">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="37f20-719">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="37f20-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="37f20-720">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="37f20-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="37f20-721">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="37f20-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="37f20-722">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="37f20-723">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="37f20-724">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="37f20-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="37f20-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="37f20-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="37f20-726">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="37f20-727">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="37f20-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="37f20-728">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="37f20-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-729">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-730">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-730">Option</span></span> | <span data-ttu-id="37f20-731">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-731">Default value</span></span> | <span data-ttu-id="37f20-732">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="37f20-733">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-734">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-734">Timeout for server activity.</span></span> <span data-ttu-id="37f20-735">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-736">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-737">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="37f20-738">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-738">15 seconds</span></span> | <span data-ttu-id="37f20-739">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-740">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-741">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-742">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-743">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-743">15 seconds</span></span> | <span data-ttu-id="37f20-744">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-745">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-746">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="37f20-747">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="37f20-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="37f20-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-749">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-749">Option</span></span> | <span data-ttu-id="37f20-750">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-750">Default value</span></span> | <span data-ttu-id="37f20-751">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="37f20-752">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-753">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-753">Timeout for server activity.</span></span> <span data-ttu-id="37f20-754">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="37f20-755">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-756">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="37f20-757">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-758">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-759">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-760">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-761">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-761">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-762">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-762">Option</span></span> | <span data-ttu-id="37f20-763">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-763">Default value</span></span> | <span data-ttu-id="37f20-764">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="37f20-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="37f20-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="37f20-766">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-767">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-767">Timeout for server activity.</span></span> <span data-ttu-id="37f20-768">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-769">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-770">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="37f20-771">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-771">15 seconds</span></span> | <span data-ttu-id="37f20-772">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-773">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-774">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-775">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="37f20-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="37f20-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="37f20-777">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-778">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-779">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-780">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="37f20-781">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-781">Configure additional options</span></span>

<span data-ttu-id="37f20-782">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="37f20-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-783">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-784">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="37f20-784">.NET Option</span></span> |  <span data-ttu-id="37f20-785">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-785">Default value</span></span> | <span data-ttu-id="37f20-786">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="37f20-787">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="37f20-788">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-789">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-790">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="37f20-791">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-791">Empty</span></span> | <span data-ttu-id="37f20-792">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="37f20-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="37f20-793">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-793">Empty</span></span> | <span data-ttu-id="37f20-794">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="37f20-795">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-795">Empty</span></span> | <span data-ttu-id="37f20-796">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="37f20-797">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-797">5 seconds</span></span> | <span data-ttu-id="37f20-798">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-798">WebSockets only.</span></span> <span data-ttu-id="37f20-799">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="37f20-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="37f20-800">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="37f20-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="37f20-801">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-801">Empty</span></span> | <span data-ttu-id="37f20-802">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="37f20-803">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="37f20-804">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="37f20-805">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="37f20-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="37f20-806">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="37f20-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="37f20-807">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="37f20-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="37f20-808">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="37f20-809">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="37f20-810">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="37f20-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="37f20-811">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="37f20-812">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="37f20-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="37f20-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-814">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-814">JavaScript Option</span></span> | <span data-ttu-id="37f20-815">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-815">Default Value</span></span> | <span data-ttu-id="37f20-816">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="37f20-817">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="37f20-818">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-819">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-820">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-821">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-821">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-822">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="37f20-822">Java Option</span></span> | <span data-ttu-id="37f20-823">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-823">Default Value</span></span> | <span data-ttu-id="37f20-824">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="37f20-825">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="37f20-826">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-827">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-828">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="37f20-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="37f20-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="37f20-830">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-830">Empty</span></span> | <span data-ttu-id="37f20-831">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="37f20-832">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="37f20-833">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="37f20-834">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="37f20-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="37f20-835">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="37f20-836">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="37f20-837">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="37f20-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="37f20-838">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="37f20-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="37f20-839">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después `Startup.ConfigureServices` de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="37f20-840">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="37f20-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="37f20-841">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un objeto `JsonSerializerSettings` JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="37f20-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="37f20-842">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="37f20-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="37f20-843">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", `Startup.ConfigureServices`use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="37f20-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="37f20-844">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="37f20-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="37f20-845">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="37f20-846">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="37f20-847">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-847">MessagePack serialization options</span></span>

<span data-ttu-id="37f20-848">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="37f20-849">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="37f20-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-850">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="37f20-851">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="37f20-851">Configure server options</span></span>

<span data-ttu-id="37f20-852">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="37f20-853">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-853">Option</span></span> | <span data-ttu-id="37f20-854">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-854">Default Value</span></span> | <span data-ttu-id="37f20-855">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="37f20-856">30 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-856">30 seconds</span></span> | <span data-ttu-id="37f20-857">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="37f20-858">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="37f20-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="37f20-859">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="37f20-860">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-860">15 seconds</span></span> | <span data-ttu-id="37f20-861">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="37f20-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="37f20-862">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-863">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-864">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-864">15 seconds</span></span> | <span data-ttu-id="37f20-865">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="37f20-866">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="37f20-867">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="37f20-868">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="37f20-868">All installed protocols</span></span> | <span data-ttu-id="37f20-869">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-869">Protocols supported by this hub.</span></span> <span data-ttu-id="37f20-870">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="37f20-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="37f20-871">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="37f20-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="37f20-872">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="37f20-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="37f20-873">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="37f20-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="37f20-874">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="37f20-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="37f20-875">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="37f20-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="37f20-876">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="37f20-877">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="37f20-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="37f20-878">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="37f20-879">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-879">Option</span></span> | <span data-ttu-id="37f20-880">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-880">Default Value</span></span> | <span data-ttu-id="37f20-881">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="37f20-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-882">32 KB</span></span> | <span data-ttu-id="37f20-883">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="37f20-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="37f20-884">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="37f20-885">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="37f20-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="37f20-886">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="37f20-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-887">32 KB</span></span> | <span data-ttu-id="37f20-888">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="37f20-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="37f20-889">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="37f20-890">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="37f20-890">All Transports are enabled.</span></span> | <span data-ttu-id="37f20-891">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="37f20-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="37f20-892">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-892">See below.</span></span> | <span data-ttu-id="37f20-893">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="37f20-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="37f20-894">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-894">See below.</span></span> | <span data-ttu-id="37f20-895">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="37f20-896">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="37f20-897">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-897">Option</span></span> | <span data-ttu-id="37f20-898">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-898">Default Value</span></span> | <span data-ttu-id="37f20-899">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="37f20-900">90 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-900">90 seconds</span></span> | <span data-ttu-id="37f20-901">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="37f20-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="37f20-902">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="37f20-903">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="37f20-904">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-904">Option</span></span> | <span data-ttu-id="37f20-905">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-905">Default Value</span></span> | <span data-ttu-id="37f20-906">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="37f20-907">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-907">5 seconds</span></span> | <span data-ttu-id="37f20-908">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="37f20-909">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="37f20-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="37f20-910">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="37f20-911">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="37f20-911">Configure client options</span></span>

<span data-ttu-id="37f20-912">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="37f20-913">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="37f20-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="37f20-914">registro</span><span class="sxs-lookup"><span data-stu-id="37f20-914">Configure logging</span></span>

<span data-ttu-id="37f20-915">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="37f20-916">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="37f20-917">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="37f20-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-918">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="37f20-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="37f20-919">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="37f20-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="37f20-920">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="37f20-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="37f20-921">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="37f20-922">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="37f20-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="37f20-923">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="37f20-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="37f20-924">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="37f20-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="37f20-925">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="37f20-926">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="37f20-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="37f20-927">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="37f20-928">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="37f20-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="37f20-929">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="37f20-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="37f20-930">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="37f20-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="37f20-931">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="37f20-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="37f20-932">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="37f20-932">Configure allowed transports</span></span>

<span data-ttu-id="37f20-933">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="37f20-934">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="37f20-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="37f20-935">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="37f20-935">All transports are enabled by default.</span></span>

<span data-ttu-id="37f20-936">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="37f20-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="37f20-937">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="37f20-938">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="37f20-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="37f20-939">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="37f20-939">Configure bearer authentication</span></span>

<span data-ttu-id="37f20-940">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="37f20-941">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="37f20-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="37f20-942">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="37f20-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="37f20-943">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="37f20-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="37f20-944">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="37f20-945">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="37f20-946">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="37f20-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="37f20-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="37f20-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="37f20-948">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="37f20-949">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="37f20-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="37f20-950">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="37f20-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-951">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-952">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-952">Option</span></span> | <span data-ttu-id="37f20-953">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-953">Default value</span></span> | <span data-ttu-id="37f20-954">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="37f20-955">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-956">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-956">Timeout for server activity.</span></span> <span data-ttu-id="37f20-957">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-958">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-959">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="37f20-960">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-960">15 seconds</span></span> | <span data-ttu-id="37f20-961">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-962">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-963">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-964">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-965">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-965">15 seconds</span></span> | <span data-ttu-id="37f20-966">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-967">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-968">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="37f20-969">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="37f20-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="37f20-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-971">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-971">Option</span></span> | <span data-ttu-id="37f20-972">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-972">Default value</span></span> | <span data-ttu-id="37f20-973">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="37f20-974">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-975">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-975">Timeout for server activity.</span></span> <span data-ttu-id="37f20-976">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="37f20-977">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-978">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="37f20-979">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-980">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-981">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-982">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-983">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-983">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-984">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-984">Option</span></span> | <span data-ttu-id="37f20-985">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-985">Default value</span></span> | <span data-ttu-id="37f20-986">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="37f20-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="37f20-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="37f20-988">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-989">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-989">Timeout for server activity.</span></span> <span data-ttu-id="37f20-990">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-991">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-992">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="37f20-993">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-993">15 seconds</span></span> | <span data-ttu-id="37f20-994">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-995">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-996">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-997">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="37f20-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="37f20-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="37f20-999">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="37f20-1000">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="37f20-1001">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="37f20-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="37f20-1002">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="37f20-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="37f20-1003">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-1003">Configure additional options</span></span>

<span data-ttu-id="37f20-1004">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="37f20-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-1006">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="37f20-1006">.NET Option</span></span> |  <span data-ttu-id="37f20-1007">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1007">Default value</span></span> | <span data-ttu-id="37f20-1008">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="37f20-1009">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="37f20-1010">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-1011">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-1012">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="37f20-1013">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1013">Empty</span></span> | <span data-ttu-id="37f20-1014">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="37f20-1015">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1015">Empty</span></span> | <span data-ttu-id="37f20-1016">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="37f20-1017">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1017">Empty</span></span> | <span data-ttu-id="37f20-1018">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="37f20-1019">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1019">5 seconds</span></span> | <span data-ttu-id="37f20-1020">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-1020">WebSockets only.</span></span> <span data-ttu-id="37f20-1021">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="37f20-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="37f20-1022">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="37f20-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="37f20-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1023">Empty</span></span> | <span data-ttu-id="37f20-1024">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="37f20-1025">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="37f20-1026">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="37f20-1027">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="37f20-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="37f20-1028">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="37f20-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="37f20-1029">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="37f20-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="37f20-1030">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="37f20-1031">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="37f20-1032">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="37f20-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="37f20-1033">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="37f20-1034">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="37f20-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="37f20-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-1036">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-1036">JavaScript Option</span></span> | <span data-ttu-id="37f20-1037">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1037">Default Value</span></span> | <span data-ttu-id="37f20-1038">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="37f20-1039">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="37f20-1040">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-1041">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-1042">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-1043">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-1044">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="37f20-1044">Java Option</span></span> | <span data-ttu-id="37f20-1045">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1045">Default Value</span></span> | <span data-ttu-id="37f20-1046">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="37f20-1047">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="37f20-1048">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-1049">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-1050">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="37f20-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="37f20-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="37f20-1052">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1052">Empty</span></span> | <span data-ttu-id="37f20-1053">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="37f20-1054">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="37f20-1055">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="37f20-1056">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="37f20-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="37f20-1057">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="37f20-1058">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="37f20-1059">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="37f20-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="37f20-1060">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="37f20-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="37f20-1061">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después `Startup.ConfigureServices` de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="37f20-1062">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="37f20-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="37f20-1063">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un objeto `JsonSerializerSettings` JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="37f20-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="37f20-1064">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="37f20-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="37f20-1065">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", `Startup.ConfigureServices`use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="37f20-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="37f20-1066">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="37f20-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="37f20-1067">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="37f20-1068">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="37f20-1069">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="37f20-1069">MessagePack serialization options</span></span>

<span data-ttu-id="37f20-1070">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="37f20-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="37f20-1071">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="37f20-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-1072">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f20-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="37f20-1073">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="37f20-1073">Configure server options</span></span>

<span data-ttu-id="37f20-1074">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="37f20-1075">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1075">Option</span></span> | <span data-ttu-id="37f20-1076">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1076">Default Value</span></span> | <span data-ttu-id="37f20-1077">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="37f20-1078">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1078">15 seconds</span></span> | <span data-ttu-id="37f20-1079">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="37f20-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="37f20-1080">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-1081">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="37f20-1082">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1082">15 seconds</span></span> | <span data-ttu-id="37f20-1083">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="37f20-1084">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="37f20-1085">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="37f20-1086">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="37f20-1086">All installed protocols</span></span> | <span data-ttu-id="37f20-1087">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="37f20-1088">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="37f20-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="37f20-1089">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="37f20-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="37f20-1090">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="37f20-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="37f20-1091">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="37f20-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="37f20-1092">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="37f20-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="37f20-1093">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="37f20-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="37f20-1094">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="37f20-1095">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="37f20-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="37f20-1096">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="37f20-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="37f20-1097">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1097">Option</span></span> | <span data-ttu-id="37f20-1098">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1098">Default Value</span></span> | <span data-ttu-id="37f20-1099">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="37f20-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-1100">32 KB</span></span> | <span data-ttu-id="37f20-1101">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="37f20-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="37f20-1102">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="37f20-1103">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="37f20-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="37f20-1104">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="37f20-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="37f20-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="37f20-1105">32 KB</span></span> | <span data-ttu-id="37f20-1106">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="37f20-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="37f20-1107">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="37f20-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="37f20-1108">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="37f20-1108">All Transports are enabled.</span></span> | <span data-ttu-id="37f20-1109">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="37f20-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="37f20-1110">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1110">See below.</span></span> | <span data-ttu-id="37f20-1111">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="37f20-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="37f20-1112">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1112">See below.</span></span> | <span data-ttu-id="37f20-1113">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="37f20-1114">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="37f20-1115">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1115">Option</span></span> | <span data-ttu-id="37f20-1116">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1116">Default Value</span></span> | <span data-ttu-id="37f20-1117">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="37f20-1118">90 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1118">90 seconds</span></span> | <span data-ttu-id="37f20-1119">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="37f20-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="37f20-1120">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="37f20-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="37f20-1121">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="37f20-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="37f20-1122">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1122">Option</span></span> | <span data-ttu-id="37f20-1123">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1123">Default Value</span></span> | <span data-ttu-id="37f20-1124">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="37f20-1125">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1125">5 seconds</span></span> | <span data-ttu-id="37f20-1126">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="37f20-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="37f20-1127">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="37f20-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="37f20-1128">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="37f20-1129">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="37f20-1129">Configure client options</span></span>

<span data-ttu-id="37f20-1130">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="37f20-1131">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="37f20-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="37f20-1132">registro</span><span class="sxs-lookup"><span data-stu-id="37f20-1132">Configure logging</span></span>

<span data-ttu-id="37f20-1133">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="37f20-1134">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="37f20-1135">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="37f20-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="37f20-1136">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="37f20-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="37f20-1137">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="37f20-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="37f20-1138">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="37f20-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="37f20-1139">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="37f20-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="37f20-1140">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="37f20-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="37f20-1141">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="37f20-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="37f20-1142">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="37f20-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="37f20-1143">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="37f20-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="37f20-1144">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="37f20-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="37f20-1145">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="37f20-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="37f20-1146">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="37f20-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="37f20-1147">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="37f20-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="37f20-1148">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="37f20-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="37f20-1149">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="37f20-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="37f20-1150">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="37f20-1150">Configure allowed transports</span></span>

<span data-ttu-id="37f20-1151">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="37f20-1152">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="37f20-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="37f20-1153">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="37f20-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="37f20-1154">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="37f20-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="37f20-1155">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="37f20-1156">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="37f20-1156">Configure bearer authentication</span></span>

<span data-ttu-id="37f20-1157">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="37f20-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="37f20-1158">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="37f20-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="37f20-1159">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="37f20-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="37f20-1160">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="37f20-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="37f20-1161">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="37f20-1162">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="37f20-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="37f20-1163">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="37f20-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="37f20-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="37f20-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="37f20-1165">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="37f20-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="37f20-1166">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="37f20-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="37f20-1167">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="37f20-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-1169">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1169">Option</span></span> | <span data-ttu-id="37f20-1170">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1170">Default value</span></span> | <span data-ttu-id="37f20-1171">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="37f20-1172">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-1173">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1173">Timeout for server activity.</span></span> <span data-ttu-id="37f20-1174">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-1175">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-1176">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="37f20-1177">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1177">15 seconds</span></span> | <span data-ttu-id="37f20-1178">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-1179">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="37f20-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="37f20-1180">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-1181">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="37f20-1182">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="37f20-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="37f20-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-1184">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1184">Option</span></span> | <span data-ttu-id="37f20-1185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1185">Default value</span></span> | <span data-ttu-id="37f20-1186">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="37f20-1187">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-1188">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1188">Timeout for server activity.</span></span> <span data-ttu-id="37f20-1189">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="37f20-1190">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-1191">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-1192">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-1193">Opción</span><span class="sxs-lookup"><span data-stu-id="37f20-1193">Option</span></span> | <span data-ttu-id="37f20-1194">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1194">Default value</span></span> | <span data-ttu-id="37f20-1195">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="37f20-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="37f20-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="37f20-1197">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="37f20-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="37f20-1198">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1198">Timeout for server activity.</span></span> <span data-ttu-id="37f20-1199">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-1200">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="37f20-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="37f20-1201">El valor recomendado es un número al menos el doble del valor `KeepAliveInterval` del servidor, para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="37f20-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="37f20-1202">15 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1202">15 seconds</span></span> | <span data-ttu-id="37f20-1203">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="37f20-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="37f20-1204">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="37f20-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="37f20-1205">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="37f20-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="37f20-1206">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="37f20-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="37f20-1207">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-1207">Configure additional options</span></span>

<span data-ttu-id="37f20-1208">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="37f20-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="37f20-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="37f20-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="37f20-1210">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="37f20-1210">.NET Option</span></span> |  <span data-ttu-id="37f20-1211">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1211">Default value</span></span> | <span data-ttu-id="37f20-1212">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="37f20-1213">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="37f20-1214">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-1215">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-1216">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="37f20-1217">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1217">Empty</span></span> | <span data-ttu-id="37f20-1218">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="37f20-1219">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1219">Empty</span></span> | <span data-ttu-id="37f20-1220">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="37f20-1221">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1221">Empty</span></span> | <span data-ttu-id="37f20-1222">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="37f20-1223">5 segundos</span><span class="sxs-lookup"><span data-stu-id="37f20-1223">5 seconds</span></span> | <span data-ttu-id="37f20-1224">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-1224">WebSockets only.</span></span> <span data-ttu-id="37f20-1225">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="37f20-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="37f20-1226">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="37f20-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="37f20-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1227">Empty</span></span> | <span data-ttu-id="37f20-1228">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="37f20-1229">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="37f20-1230">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="37f20-1231">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="37f20-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="37f20-1232">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="37f20-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="37f20-1233">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="37f20-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="37f20-1234">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="37f20-1235">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="37f20-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="37f20-1236">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="37f20-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="37f20-1237">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="37f20-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="37f20-1238">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="37f20-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="37f20-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="37f20-1240">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f20-1240">JavaScript Option</span></span> | <span data-ttu-id="37f20-1241">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1241">Default Value</span></span> | <span data-ttu-id="37f20-1242">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="37f20-1243">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="37f20-1244">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-1245">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-1246">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="37f20-1247">Java</span><span class="sxs-lookup"><span data-stu-id="37f20-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="37f20-1248">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="37f20-1248">Java Option</span></span> | <span data-ttu-id="37f20-1249">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="37f20-1249">Default Value</span></span> | <span data-ttu-id="37f20-1250">Descripción</span><span class="sxs-lookup"><span data-stu-id="37f20-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="37f20-1251">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="37f20-1252">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="37f20-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="37f20-1253">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="37f20-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="37f20-1254">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="37f20-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="37f20-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="37f20-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="37f20-1256">Empty</span><span class="sxs-lookup"><span data-stu-id="37f20-1256">Empty</span></span> | <span data-ttu-id="37f20-1257">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="37f20-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="37f20-1258">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="37f20-1259">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="37f20-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="37f20-1260">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="37f20-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="37f20-1261">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="37f20-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
