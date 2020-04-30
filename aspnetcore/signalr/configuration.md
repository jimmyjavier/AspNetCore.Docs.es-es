---
title: Configuración SignalR de ASP.net Core
author: bradygaster
description: Aprenda a configurar ASP.NET Core SignalR aplicaciones.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558998"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="4b163-103">Configuración de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="4b163-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4b163-104">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4b163-105">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4b163-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4b163-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="4b163-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4b163-107">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4b163-108">`AddJsonProtocol`se puede agregar después [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de AddSignalR `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="4b163-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4b163-109">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="4b163-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4b163-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4b163-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4b163-111">Para obtener más información, vea la [documentación de System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="4b163-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4b163-112">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase `Startup.ConfigureServices`" predeterminados, use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="4b163-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="4b163-113">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4b163-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4b163-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4b163-115">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4b163-116">Cambiar a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="4b163-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4b163-117">Si necesita características de `Newtonsoft.Json` que no se admiten `System.Text.Json`en, consulte [cambiar a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="4b163-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4b163-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-118">MessagePack serialization options</span></span>

<span data-ttu-id="4b163-119">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4b163-120">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="4b163-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-121">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4b163-122">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="4b163-122">Configure server options</span></span>

<span data-ttu-id="4b163-123">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4b163-124">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-124">Option</span></span> | <span data-ttu-id="4b163-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-125">Default Value</span></span> | <span data-ttu-id="4b163-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4b163-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-127">30 seconds</span></span> | <span data-ttu-id="4b163-128">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4b163-129">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="4b163-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4b163-130">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4b163-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-131">15 seconds</span></span> | <span data-ttu-id="4b163-132">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="4b163-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4b163-133">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-134">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-135">15 seconds</span></span> | <span data-ttu-id="4b163-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4b163-137">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4b163-138">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4b163-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="4b163-139">All installed protocols</span></span> | <span data-ttu-id="4b163-140">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-140">Protocols supported by this hub.</span></span> <span data-ttu-id="4b163-141">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="4b163-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4b163-142">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="4b163-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4b163-143">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="4b163-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4b163-144">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4b163-145">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4b163-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-146">32 KB</span></span> | <span data-ttu-id="4b163-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="4b163-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4b163-148">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="4b163-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4b163-149">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="4b163-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4b163-150">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b163-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4b163-151">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4b163-152">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="4b163-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4b163-153">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4b163-154">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-154">Option</span></span> | <span data-ttu-id="4b163-155">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-155">Default Value</span></span> | <span data-ttu-id="4b163-156">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4b163-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-157">32 KB</span></span> | <span data-ttu-id="4b163-158">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="4b163-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4b163-159">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4b163-160">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="4b163-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4b163-161">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4b163-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-162">32 KB</span></span> | <span data-ttu-id="4b163-163">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="4b163-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4b163-164">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4b163-165">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="4b163-165">All Transports are enabled.</span></span> | <span data-ttu-id="4b163-166">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="4b163-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4b163-167">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-167">See below.</span></span> | <span data-ttu-id="4b163-168">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="4b163-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4b163-169">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-169">See below.</span></span> | <span data-ttu-id="4b163-170">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="4b163-171">0</span><span class="sxs-lookup"><span data-stu-id="4b163-171">0</span></span> | <span data-ttu-id="4b163-172">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="4b163-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="4b163-173">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="4b163-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="4b163-174">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4b163-175">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-175">Option</span></span> | <span data-ttu-id="4b163-176">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-176">Default Value</span></span> | <span data-ttu-id="4b163-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4b163-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-178">90 seconds</span></span> | <span data-ttu-id="4b163-179">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="4b163-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4b163-180">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4b163-181">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4b163-182">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-182">Option</span></span> | <span data-ttu-id="4b163-183">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-183">Default Value</span></span> | <span data-ttu-id="4b163-184">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4b163-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-185">5 seconds</span></span> | <span data-ttu-id="4b163-186">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4b163-187">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="4b163-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4b163-188">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4b163-189">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="4b163-189">Configure client options</span></span>

<span data-ttu-id="4b163-190">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4b163-191">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="4b163-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4b163-192">registro</span><span class="sxs-lookup"><span data-stu-id="4b163-192">Configure logging</span></span>

<span data-ttu-id="4b163-193">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4b163-194">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4b163-195">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="4b163-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-196">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="4b163-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4b163-197">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="4b163-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4b163-198">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b163-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4b163-199">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4b163-200">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="4b163-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4b163-201">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="4b163-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4b163-202">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="4b163-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4b163-203">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4b163-204">Esto resulta útil cuando se configura el registro de Signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="4b163-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4b163-205">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="4b163-205">The following table lists the available log levels.</span></span> <span data-ttu-id="4b163-206">El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="4b163-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4b163-207">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="4b163-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4b163-208">String</span><span class="sxs-lookup"><span data-stu-id="4b163-208">String</span></span>                      | <span data-ttu-id="4b163-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4b163-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4b163-210">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="4b163-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4b163-211">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="4b163-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4b163-212">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4b163-213">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4b163-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4b163-214">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4b163-215">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="4b163-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4b163-216">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="4b163-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4b163-217">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="4b163-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4b163-218">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="4b163-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4b163-219">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="4b163-219">Configure allowed transports</span></span>

<span data-ttu-id="4b163-220">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4b163-221">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="4b163-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4b163-222">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4b163-222">All transports are enabled by default.</span></span>

<span data-ttu-id="4b163-223">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="4b163-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4b163-224">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4b163-225">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="4b163-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4b163-226">En el cliente de Java, el transporte se selecciona con `withTransport` el método en `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4b163-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4b163-227">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4b163-228">El cliente de Signalr Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="4b163-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4b163-229">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="4b163-229">Configure bearer authentication</span></span>

<span data-ttu-id="4b163-230">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4b163-231">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="4b163-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4b163-232">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4b163-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4b163-233">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4b163-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4b163-234">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4b163-235">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4b163-236">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4b163-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4b163-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="4b163-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4b163-238">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4b163-239">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="4b163-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4b163-240">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="4b163-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-241">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-242">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-242">Option</span></span> | <span data-ttu-id="4b163-243">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-243">Default value</span></span> | <span data-ttu-id="4b163-244">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4b163-245">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-246">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-246">Timeout for server activity.</span></span> <span data-ttu-id="4b163-247">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-248">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-249">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4b163-250">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-250">15 seconds</span></span> | <span data-ttu-id="4b163-251">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-252">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-253">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-254">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-255">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-255">15 seconds</span></span> | <span data-ttu-id="4b163-256">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-257">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-258">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4b163-259">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="4b163-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4b163-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-261">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-261">Option</span></span> | <span data-ttu-id="4b163-262">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-262">Default value</span></span> | <span data-ttu-id="4b163-263">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4b163-264">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-265">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-265">Timeout for server activity.</span></span> <span data-ttu-id="4b163-266">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4b163-267">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-268">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4b163-269">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-270">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-271">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-272">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-273">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-273">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-274">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-274">Option</span></span> | <span data-ttu-id="4b163-275">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-275">Default value</span></span> | <span data-ttu-id="4b163-276">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4b163-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4b163-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4b163-278">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-279">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-279">Timeout for server activity.</span></span> <span data-ttu-id="4b163-280">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-281">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-282">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4b163-283">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-283">15 seconds</span></span> | <span data-ttu-id="4b163-284">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-285">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-286">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-287">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4b163-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4b163-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4b163-289">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-290">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-291">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-292">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4b163-293">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-293">Configure additional options</span></span>

<span data-ttu-id="4b163-294">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="4b163-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-295">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-296">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="4b163-296">.NET Option</span></span> |  <span data-ttu-id="4b163-297">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-297">Default value</span></span> | <span data-ttu-id="4b163-298">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4b163-299">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4b163-300">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-301">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-302">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4b163-303">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-303">Empty</span></span> | <span data-ttu-id="4b163-304">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4b163-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4b163-305">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-305">Empty</span></span> | <span data-ttu-id="4b163-306">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4b163-307">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-307">Empty</span></span> | <span data-ttu-id="4b163-308">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4b163-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-309">5 seconds</span></span> | <span data-ttu-id="4b163-310">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-310">WebSockets only.</span></span> <span data-ttu-id="4b163-311">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="4b163-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4b163-312">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4b163-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4b163-313">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-313">Empty</span></span> | <span data-ttu-id="4b163-314">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4b163-315">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4b163-316">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="4b163-317">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="4b163-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4b163-318">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="4b163-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4b163-319">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="4b163-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4b163-320">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4b163-321">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4b163-322">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4b163-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4b163-323">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4b163-324">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="4b163-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4b163-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-326">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-326">JavaScript Option</span></span> | <span data-ttu-id="4b163-327">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-327">Default Value</span></span> | <span data-ttu-id="4b163-328">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4b163-329">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4b163-330">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-331">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-332">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="4b163-333">Especifica si las credenciales se enviarán con la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="4b163-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="4b163-334">Azure App Service usa cookies para sesiones permanentes y necesita que esta opción esté habilitada para funcionar correctamente.</span><span class="sxs-lookup"><span data-stu-id="4b163-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="4b163-335">Para obtener más información sobre CORS con Signalr, <xref:signalr/security#cross-origin-resource-sharing>consulte.</span><span class="sxs-lookup"><span data-stu-id="4b163-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-336">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-336">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-337">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="4b163-337">Java Option</span></span> | <span data-ttu-id="4b163-338">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-338">Default Value</span></span> | <span data-ttu-id="4b163-339">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4b163-340">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4b163-341">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-342">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-343">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4b163-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4b163-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4b163-345">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-345">Empty</span></span> | <span data-ttu-id="4b163-346">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4b163-347">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4b163-348">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4b163-349">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4b163-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4b163-350">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4b163-351">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4b163-352">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4b163-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4b163-353">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="4b163-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4b163-354">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4b163-355">`AddJsonProtocol`se puede agregar después [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de AddSignalR `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="4b163-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4b163-356">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="4b163-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4b163-357">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4b163-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4b163-358">Para obtener más información, vea la [documentación de System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="4b163-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4b163-359">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase `Startup.ConfigureServices`" predeterminados, use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="4b163-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="4b163-360">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4b163-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4b163-361">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4b163-362">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4b163-363">Cambiar a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="4b163-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4b163-364">Si necesita características de `Newtonsoft.Json` que no se admiten `System.Text.Json`en, consulte [cambiar a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="4b163-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4b163-365">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-365">MessagePack serialization options</span></span>

<span data-ttu-id="4b163-366">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4b163-367">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="4b163-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-368">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4b163-369">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="4b163-369">Configure server options</span></span>

<span data-ttu-id="4b163-370">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4b163-371">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-371">Option</span></span> | <span data-ttu-id="4b163-372">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-372">Default Value</span></span> | <span data-ttu-id="4b163-373">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4b163-374">30 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-374">30 seconds</span></span> | <span data-ttu-id="4b163-375">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4b163-376">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="4b163-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4b163-377">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4b163-378">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-378">15 seconds</span></span> | <span data-ttu-id="4b163-379">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="4b163-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4b163-380">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-381">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-382">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-382">15 seconds</span></span> | <span data-ttu-id="4b163-383">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4b163-384">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4b163-385">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4b163-386">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="4b163-386">All installed protocols</span></span> | <span data-ttu-id="4b163-387">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-387">Protocols supported by this hub.</span></span> <span data-ttu-id="4b163-388">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="4b163-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4b163-389">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="4b163-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4b163-390">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="4b163-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4b163-391">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4b163-392">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4b163-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-393">32 KB</span></span> | <span data-ttu-id="4b163-394">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="4b163-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4b163-395">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="4b163-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4b163-396">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="4b163-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4b163-397">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b163-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4b163-398">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4b163-399">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="4b163-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4b163-400">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4b163-401">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-401">Option</span></span> | <span data-ttu-id="4b163-402">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-402">Default Value</span></span> | <span data-ttu-id="4b163-403">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4b163-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-404">32 KB</span></span> | <span data-ttu-id="4b163-405">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="4b163-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4b163-406">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4b163-407">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="4b163-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4b163-408">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4b163-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-409">32 KB</span></span> | <span data-ttu-id="4b163-410">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="4b163-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4b163-411">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4b163-412">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="4b163-412">All Transports are enabled.</span></span> | <span data-ttu-id="4b163-413">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="4b163-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4b163-414">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-414">See below.</span></span> | <span data-ttu-id="4b163-415">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="4b163-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4b163-416">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-416">See below.</span></span> | <span data-ttu-id="4b163-417">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="4b163-418">0</span><span class="sxs-lookup"><span data-stu-id="4b163-418">0</span></span> | <span data-ttu-id="4b163-419">Especifique la versión mínima del protocolo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="4b163-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="4b163-420">Se utiliza para limitar a los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="4b163-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="4b163-421">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4b163-422">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-422">Option</span></span> | <span data-ttu-id="4b163-423">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-423">Default Value</span></span> | <span data-ttu-id="4b163-424">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4b163-425">90 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-425">90 seconds</span></span> | <span data-ttu-id="4b163-426">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="4b163-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4b163-427">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4b163-428">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4b163-429">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-429">Option</span></span> | <span data-ttu-id="4b163-430">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-430">Default Value</span></span> | <span data-ttu-id="4b163-431">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4b163-432">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-432">5 seconds</span></span> | <span data-ttu-id="4b163-433">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4b163-434">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="4b163-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4b163-435">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4b163-436">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="4b163-436">Configure client options</span></span>

<span data-ttu-id="4b163-437">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4b163-438">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="4b163-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4b163-439">registro</span><span class="sxs-lookup"><span data-stu-id="4b163-439">Configure logging</span></span>

<span data-ttu-id="4b163-440">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4b163-441">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4b163-442">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="4b163-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-443">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="4b163-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4b163-444">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="4b163-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4b163-445">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b163-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4b163-446">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4b163-447">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="4b163-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4b163-448">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="4b163-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4b163-449">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="4b163-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4b163-450">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4b163-451">Esto resulta útil cuando se configura el registro de Signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="4b163-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4b163-452">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="4b163-452">The following table lists the available log levels.</span></span> <span data-ttu-id="4b163-453">El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="4b163-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4b163-454">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="4b163-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4b163-455">String</span><span class="sxs-lookup"><span data-stu-id="4b163-455">String</span></span>                      | <span data-ttu-id="4b163-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4b163-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4b163-457">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="4b163-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4b163-458">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="4b163-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4b163-459">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4b163-460">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4b163-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4b163-461">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4b163-462">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="4b163-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4b163-463">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="4b163-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4b163-464">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="4b163-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4b163-465">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="4b163-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4b163-466">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="4b163-466">Configure allowed transports</span></span>

<span data-ttu-id="4b163-467">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4b163-468">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="4b163-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4b163-469">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4b163-469">All transports are enabled by default.</span></span>

<span data-ttu-id="4b163-470">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="4b163-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4b163-471">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4b163-472">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="4b163-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4b163-473">En el cliente de Java, el transporte se selecciona con `withTransport` el método en `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4b163-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4b163-474">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4b163-475">El cliente de Signalr Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="4b163-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4b163-476">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="4b163-476">Configure bearer authentication</span></span>

<span data-ttu-id="4b163-477">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4b163-478">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="4b163-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4b163-479">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4b163-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4b163-480">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4b163-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4b163-481">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4b163-482">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4b163-483">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4b163-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4b163-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="4b163-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4b163-485">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4b163-486">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="4b163-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4b163-487">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="4b163-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-488">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-489">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-489">Option</span></span> | <span data-ttu-id="4b163-490">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-490">Default value</span></span> | <span data-ttu-id="4b163-491">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4b163-492">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-493">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-493">Timeout for server activity.</span></span> <span data-ttu-id="4b163-494">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-495">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-496">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4b163-497">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-497">15 seconds</span></span> | <span data-ttu-id="4b163-498">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-499">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-500">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-501">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-502">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-502">15 seconds</span></span> | <span data-ttu-id="4b163-503">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-504">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-505">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4b163-506">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="4b163-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4b163-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-508">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-508">Option</span></span> | <span data-ttu-id="4b163-509">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-509">Default value</span></span> | <span data-ttu-id="4b163-510">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4b163-511">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-512">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-512">Timeout for server activity.</span></span> <span data-ttu-id="4b163-513">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4b163-514">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-515">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4b163-516">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-517">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-518">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-519">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-520">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-520">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-521">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-521">Option</span></span> | <span data-ttu-id="4b163-522">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-522">Default value</span></span> | <span data-ttu-id="4b163-523">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4b163-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4b163-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4b163-525">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-526">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-526">Timeout for server activity.</span></span> <span data-ttu-id="4b163-527">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-528">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-529">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4b163-530">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-530">15 seconds</span></span> | <span data-ttu-id="4b163-531">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-532">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-533">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-534">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4b163-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4b163-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4b163-536">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-537">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-538">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-539">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4b163-540">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-540">Configure additional options</span></span>

<span data-ttu-id="4b163-541">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="4b163-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-542">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-543">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="4b163-543">.NET Option</span></span> |  <span data-ttu-id="4b163-544">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-544">Default value</span></span> | <span data-ttu-id="4b163-545">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4b163-546">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4b163-547">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-548">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-549">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4b163-550">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-550">Empty</span></span> | <span data-ttu-id="4b163-551">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4b163-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4b163-552">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-552">Empty</span></span> | <span data-ttu-id="4b163-553">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4b163-554">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-554">Empty</span></span> | <span data-ttu-id="4b163-555">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4b163-556">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-556">5 seconds</span></span> | <span data-ttu-id="4b163-557">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-557">WebSockets only.</span></span> <span data-ttu-id="4b163-558">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="4b163-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4b163-559">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4b163-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4b163-560">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-560">Empty</span></span> | <span data-ttu-id="4b163-561">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4b163-562">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4b163-563">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="4b163-564">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="4b163-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4b163-565">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="4b163-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4b163-566">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="4b163-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4b163-567">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4b163-568">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4b163-569">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4b163-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4b163-570">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4b163-571">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="4b163-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4b163-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-573">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-573">JavaScript Option</span></span> | <span data-ttu-id="4b163-574">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-574">Default Value</span></span> | <span data-ttu-id="4b163-575">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4b163-576">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4b163-577">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-578">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-579">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-580">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-580">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-581">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="4b163-581">Java Option</span></span> | <span data-ttu-id="4b163-582">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-582">Default Value</span></span> | <span data-ttu-id="4b163-583">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4b163-584">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4b163-585">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-586">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-587">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4b163-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4b163-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4b163-589">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-589">Empty</span></span> | <span data-ttu-id="4b163-590">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4b163-591">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4b163-592">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4b163-593">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4b163-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4b163-594">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4b163-595">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4b163-596">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4b163-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4b163-597">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="4b163-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4b163-598">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4b163-599">`AddJsonProtocol`se puede agregar después [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) de AddSignalR `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="4b163-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4b163-600">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="4b163-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4b163-601">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de ese objeto es un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objeto que se puede utilizar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4b163-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4b163-602">Para obtener más información, vea la [documentación de System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="4b163-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4b163-603">Por ejemplo, para configurar el serializador para que no cambie el uso de mayúsculas y minúsculas de los nombres de propiedad, en lugar de los nombres "camelCase `Startup.ConfigureServices`" predeterminados, use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="4b163-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="4b163-604">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4b163-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4b163-605">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4b163-606">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4b163-607">Cambiar a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="4b163-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4b163-608">Si necesita características de `Newtonsoft.Json` que no se admiten `System.Text.Json`en, consulte [cambiar a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="4b163-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4b163-609">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-609">MessagePack serialization options</span></span>

<span data-ttu-id="4b163-610">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4b163-611">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="4b163-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-612">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4b163-613">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="4b163-613">Configure server options</span></span>

<span data-ttu-id="4b163-614">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4b163-615">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-615">Option</span></span> | <span data-ttu-id="4b163-616">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-616">Default Value</span></span> | <span data-ttu-id="4b163-617">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4b163-618">30 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-618">30 seconds</span></span> | <span data-ttu-id="4b163-619">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4b163-620">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="4b163-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4b163-621">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4b163-622">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-622">15 seconds</span></span> | <span data-ttu-id="4b163-623">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="4b163-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4b163-624">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-625">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-626">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-626">15 seconds</span></span> | <span data-ttu-id="4b163-627">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4b163-628">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4b163-629">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4b163-630">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="4b163-630">All installed protocols</span></span> | <span data-ttu-id="4b163-631">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-631">Protocols supported by this hub.</span></span> <span data-ttu-id="4b163-632">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="4b163-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4b163-633">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="4b163-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4b163-634">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="4b163-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4b163-635">Número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4b163-636">Si se alcanza este límite, el procesamiento de las invocaciones se bloquea hasta que el servidor procesa los elementos de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4b163-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-637">32 KB</span></span> | <span data-ttu-id="4b163-638">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="4b163-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4b163-639">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="4b163-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4b163-640">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="4b163-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4b163-641">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b163-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4b163-642">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4b163-643">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="4b163-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4b163-644">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4b163-645">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-645">Option</span></span> | <span data-ttu-id="4b163-646">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-646">Default Value</span></span> | <span data-ttu-id="4b163-647">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4b163-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-648">32 KB</span></span> | <span data-ttu-id="4b163-649">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la presión de reserva.</span><span class="sxs-lookup"><span data-stu-id="4b163-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4b163-650">Aumentar este valor permite que el servidor reciba mensajes más grandes con más rapidez sin aplicar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4b163-651">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="4b163-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4b163-652">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4b163-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-653">32 KB</span></span> | <span data-ttu-id="4b163-654">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="4b163-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4b163-655">El aumento de este valor permite que el servidor almacene en búfer mensajes más grandes con más rapidez sin esperar la presión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4b163-656">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="4b163-656">All Transports are enabled.</span></span> | <span data-ttu-id="4b163-657">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="4b163-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4b163-658">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-658">See below.</span></span> | <span data-ttu-id="4b163-659">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="4b163-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4b163-660">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-660">See below.</span></span> | <span data-ttu-id="4b163-661">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4b163-662">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4b163-663">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-663">Option</span></span> | <span data-ttu-id="4b163-664">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-664">Default Value</span></span> | <span data-ttu-id="4b163-665">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4b163-666">90 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-666">90 seconds</span></span> | <span data-ttu-id="4b163-667">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="4b163-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4b163-668">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4b163-669">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4b163-670">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-670">Option</span></span> | <span data-ttu-id="4b163-671">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-671">Default Value</span></span> | <span data-ttu-id="4b163-672">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4b163-673">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-673">5 seconds</span></span> | <span data-ttu-id="4b163-674">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4b163-675">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="4b163-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4b163-676">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4b163-677">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="4b163-677">Configure client options</span></span>

<span data-ttu-id="4b163-678">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4b163-679">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="4b163-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4b163-680">registro</span><span class="sxs-lookup"><span data-stu-id="4b163-680">Configure logging</span></span>

<span data-ttu-id="4b163-681">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4b163-682">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4b163-683">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="4b163-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-684">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="4b163-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4b163-685">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="4b163-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4b163-686">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b163-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4b163-687">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4b163-688">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="4b163-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4b163-689">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="4b163-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4b163-690">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="4b163-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4b163-691">En lugar de un `LogLevel` valor, también puede proporcionar un `string` valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4b163-692">Esto resulta útil cuando se configura el registro de Signalr en entornos donde no se tiene `LogLevel` acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="4b163-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4b163-693">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="4b163-693">The following table lists the available log levels.</span></span> <span data-ttu-id="4b163-694">El valor que se proporciona `configureLogging` para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="4b163-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4b163-695">Se registrarán los mensajes registrados en este nivel, **o los niveles que se enumeran en la tabla**.</span><span class="sxs-lookup"><span data-stu-id="4b163-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4b163-696">String</span><span class="sxs-lookup"><span data-stu-id="4b163-696">String</span></span>                      | <span data-ttu-id="4b163-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4b163-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4b163-698">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="4b163-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4b163-699">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="4b163-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4b163-700">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4b163-701">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4b163-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4b163-702">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4b163-703">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="4b163-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4b163-704">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="4b163-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4b163-705">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="4b163-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4b163-706">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="4b163-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4b163-707">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="4b163-707">Configure allowed transports</span></span>

<span data-ttu-id="4b163-708">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4b163-709">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="4b163-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4b163-710">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4b163-710">All transports are enabled by default.</span></span>

<span data-ttu-id="4b163-711">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="4b163-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4b163-712">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4b163-713">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="4b163-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4b163-714">En el cliente de Java, el transporte se selecciona con `withTransport` el método en `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="4b163-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4b163-715">De forma predeterminada, el cliente de Java usa el transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4b163-716">El cliente de Signalr Java no admite todavía la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="4b163-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4b163-717">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="4b163-717">Configure bearer authentication</span></span>

<span data-ttu-id="4b163-718">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4b163-719">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="4b163-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4b163-720">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4b163-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4b163-721">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4b163-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4b163-722">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4b163-723">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4b163-724">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4b163-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4b163-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="4b163-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4b163-726">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4b163-727">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="4b163-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4b163-728">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="4b163-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-729">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-730">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-730">Option</span></span> | <span data-ttu-id="4b163-731">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-731">Default value</span></span> | <span data-ttu-id="4b163-732">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4b163-733">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-734">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-734">Timeout for server activity.</span></span> <span data-ttu-id="4b163-735">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-736">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-737">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4b163-738">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-738">15 seconds</span></span> | <span data-ttu-id="4b163-739">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-740">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-741">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-742">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-743">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-743">15 seconds</span></span> | <span data-ttu-id="4b163-744">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-745">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-746">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4b163-747">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="4b163-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4b163-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-749">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-749">Option</span></span> | <span data-ttu-id="4b163-750">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-750">Default value</span></span> | <span data-ttu-id="4b163-751">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4b163-752">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-753">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-753">Timeout for server activity.</span></span> <span data-ttu-id="4b163-754">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4b163-755">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-756">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4b163-757">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-758">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-759">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-760">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-761">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-761">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-762">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-762">Option</span></span> | <span data-ttu-id="4b163-763">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-763">Default value</span></span> | <span data-ttu-id="4b163-764">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4b163-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4b163-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4b163-766">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-767">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-767">Timeout for server activity.</span></span> <span data-ttu-id="4b163-768">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-769">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-770">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4b163-771">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-771">15 seconds</span></span> | <span data-ttu-id="4b163-772">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-773">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-774">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-775">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4b163-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4b163-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4b163-777">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-778">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-779">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-780">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4b163-781">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-781">Configure additional options</span></span>

<span data-ttu-id="4b163-782">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="4b163-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-783">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-784">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="4b163-784">.NET Option</span></span> |  <span data-ttu-id="4b163-785">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-785">Default value</span></span> | <span data-ttu-id="4b163-786">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4b163-787">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4b163-788">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-789">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-790">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4b163-791">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-791">Empty</span></span> | <span data-ttu-id="4b163-792">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4b163-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4b163-793">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-793">Empty</span></span> | <span data-ttu-id="4b163-794">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4b163-795">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-795">Empty</span></span> | <span data-ttu-id="4b163-796">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4b163-797">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-797">5 seconds</span></span> | <span data-ttu-id="4b163-798">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-798">WebSockets only.</span></span> <span data-ttu-id="4b163-799">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="4b163-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4b163-800">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4b163-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4b163-801">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-801">Empty</span></span> | <span data-ttu-id="4b163-802">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4b163-803">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4b163-804">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="4b163-805">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="4b163-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4b163-806">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="4b163-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4b163-807">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="4b163-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4b163-808">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4b163-809">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4b163-810">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4b163-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4b163-811">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4b163-812">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="4b163-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4b163-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-814">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-814">JavaScript Option</span></span> | <span data-ttu-id="4b163-815">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-815">Default Value</span></span> | <span data-ttu-id="4b163-816">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4b163-817">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4b163-818">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-819">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-820">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-821">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-821">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-822">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="4b163-822">Java Option</span></span> | <span data-ttu-id="4b163-823">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-823">Default Value</span></span> | <span data-ttu-id="4b163-824">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4b163-825">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4b163-826">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-827">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-828">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4b163-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4b163-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4b163-830">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-830">Empty</span></span> | <span data-ttu-id="4b163-831">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4b163-832">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4b163-833">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4b163-834">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4b163-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4b163-835">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4b163-836">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4b163-837">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4b163-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4b163-838">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="4b163-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4b163-839">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después `Startup.ConfigureServices` de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4b163-840">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="4b163-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4b163-841">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un objeto `JsonSerializerSettings` JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4b163-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4b163-842">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="4b163-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="4b163-843">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", `Startup.ConfigureServices`use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="4b163-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="4b163-844">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4b163-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4b163-845">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4b163-846">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4b163-847">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-847">MessagePack serialization options</span></span>

<span data-ttu-id="4b163-848">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4b163-849">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="4b163-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-850">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4b163-851">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="4b163-851">Configure server options</span></span>

<span data-ttu-id="4b163-852">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4b163-853">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-853">Option</span></span> | <span data-ttu-id="4b163-854">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-854">Default Value</span></span> | <span data-ttu-id="4b163-855">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4b163-856">30 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-856">30 seconds</span></span> | <span data-ttu-id="4b163-857">El servidor considerará que el cliente se ha desconectado Si no ha recibido un mensaje (incluido Keep-Alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4b163-858">Podría tardar más de este intervalo de tiempo de espera para que el cliente se marque realmente como desconectado, debido a la manera en que se implementa.</span><span class="sxs-lookup"><span data-stu-id="4b163-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4b163-859">El valor recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4b163-860">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-860">15 seconds</span></span> | <span data-ttu-id="4b163-861">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="4b163-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4b163-862">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-863">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-864">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-864">15 seconds</span></span> | <span data-ttu-id="4b163-865">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4b163-866">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4b163-867">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4b163-868">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="4b163-868">All installed protocols</span></span> | <span data-ttu-id="4b163-869">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-869">Protocols supported by this hub.</span></span> <span data-ttu-id="4b163-870">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="4b163-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4b163-871">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="4b163-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4b163-872">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="4b163-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4b163-873">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="4b163-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4b163-874">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="4b163-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4b163-875">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b163-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4b163-876">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4b163-877">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="4b163-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4b163-878">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4b163-879">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-879">Option</span></span> | <span data-ttu-id="4b163-880">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-880">Default Value</span></span> | <span data-ttu-id="4b163-881">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4b163-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-882">32 KB</span></span> | <span data-ttu-id="4b163-883">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="4b163-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4b163-884">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4b163-885">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="4b163-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4b163-886">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4b163-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-887">32 KB</span></span> | <span data-ttu-id="4b163-888">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="4b163-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4b163-889">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4b163-890">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="4b163-890">All Transports are enabled.</span></span> | <span data-ttu-id="4b163-891">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="4b163-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4b163-892">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-892">See below.</span></span> | <span data-ttu-id="4b163-893">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="4b163-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4b163-894">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-894">See below.</span></span> | <span data-ttu-id="4b163-895">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4b163-896">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4b163-897">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-897">Option</span></span> | <span data-ttu-id="4b163-898">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-898">Default Value</span></span> | <span data-ttu-id="4b163-899">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4b163-900">90 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-900">90 seconds</span></span> | <span data-ttu-id="4b163-901">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="4b163-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4b163-902">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4b163-903">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4b163-904">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-904">Option</span></span> | <span data-ttu-id="4b163-905">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-905">Default Value</span></span> | <span data-ttu-id="4b163-906">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4b163-907">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-907">5 seconds</span></span> | <span data-ttu-id="4b163-908">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4b163-909">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="4b163-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4b163-910">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4b163-911">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="4b163-911">Configure client options</span></span>

<span data-ttu-id="4b163-912">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4b163-913">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="4b163-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4b163-914">registro</span><span class="sxs-lookup"><span data-stu-id="4b163-914">Configure logging</span></span>

<span data-ttu-id="4b163-915">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4b163-916">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4b163-917">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="4b163-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-918">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="4b163-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4b163-919">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="4b163-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4b163-920">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b163-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4b163-921">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4b163-922">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="4b163-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4b163-923">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="4b163-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4b163-924">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="4b163-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4b163-925">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4b163-926">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4b163-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4b163-927">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4b163-928">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="4b163-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4b163-929">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="4b163-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4b163-930">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="4b163-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4b163-931">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="4b163-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4b163-932">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="4b163-932">Configure allowed transports</span></span>

<span data-ttu-id="4b163-933">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4b163-934">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="4b163-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4b163-935">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4b163-935">All transports are enabled by default.</span></span>

<span data-ttu-id="4b163-936">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="4b163-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4b163-937">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4b163-938">En esta versión del cliente de Java, WebSockets es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="4b163-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4b163-939">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="4b163-939">Configure bearer authentication</span></span>

<span data-ttu-id="4b163-940">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4b163-941">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="4b163-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4b163-942">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4b163-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4b163-943">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4b163-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4b163-944">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4b163-945">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4b163-946">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4b163-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4b163-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="4b163-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4b163-948">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4b163-949">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="4b163-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4b163-950">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="4b163-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-951">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-952">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-952">Option</span></span> | <span data-ttu-id="4b163-953">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-953">Default value</span></span> | <span data-ttu-id="4b163-954">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4b163-955">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-956">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-956">Timeout for server activity.</span></span> <span data-ttu-id="4b163-957">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-958">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-959">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4b163-960">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-960">15 seconds</span></span> | <span data-ttu-id="4b163-961">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-962">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-963">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-964">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-965">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-965">15 seconds</span></span> | <span data-ttu-id="4b163-966">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-967">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-968">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4b163-969">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="4b163-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4b163-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-971">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-971">Option</span></span> | <span data-ttu-id="4b163-972">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-972">Default value</span></span> | <span data-ttu-id="4b163-973">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4b163-974">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-975">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-975">Timeout for server activity.</span></span> <span data-ttu-id="4b163-976">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4b163-977">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-978">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4b163-979">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-980">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-981">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-982">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-983">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-983">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-984">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-984">Option</span></span> | <span data-ttu-id="4b163-985">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-985">Default value</span></span> | <span data-ttu-id="4b163-986">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4b163-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4b163-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4b163-988">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-989">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-989">Timeout for server activity.</span></span> <span data-ttu-id="4b163-990">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-991">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-992">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4b163-993">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-993">15 seconds</span></span> | <span data-ttu-id="4b163-994">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-995">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-996">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-997">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4b163-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4b163-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4b163-999">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4b163-1000">Determina el intervalo en el que el cliente envía mensajes de ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4b163-1001">Al enviar cualquier mensaje desde el cliente, se restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="4b163-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4b163-1002">Si el cliente no ha enviado un mensaje en `ClientTimeoutInterval` el conjunto del servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="4b163-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4b163-1003">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-1003">Configure additional options</span></span>

<span data-ttu-id="4b163-1004">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="4b163-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-1006">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="4b163-1006">.NET Option</span></span> |  <span data-ttu-id="4b163-1007">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1007">Default value</span></span> | <span data-ttu-id="4b163-1008">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4b163-1009">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4b163-1010">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-1011">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-1012">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4b163-1013">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1013">Empty</span></span> | <span data-ttu-id="4b163-1014">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4b163-1015">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1015">Empty</span></span> | <span data-ttu-id="4b163-1016">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4b163-1017">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1017">Empty</span></span> | <span data-ttu-id="4b163-1018">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4b163-1019">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1019">5 seconds</span></span> | <span data-ttu-id="4b163-1020">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-1020">WebSockets only.</span></span> <span data-ttu-id="4b163-1021">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="4b163-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4b163-1022">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4b163-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4b163-1023">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1023">Empty</span></span> | <span data-ttu-id="4b163-1024">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4b163-1025">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4b163-1026">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="4b163-1027">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="4b163-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4b163-1028">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="4b163-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4b163-1029">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="4b163-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4b163-1030">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4b163-1031">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4b163-1032">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4b163-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4b163-1033">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4b163-1034">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="4b163-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4b163-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-1036">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-1036">JavaScript Option</span></span> | <span data-ttu-id="4b163-1037">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1037">Default Value</span></span> | <span data-ttu-id="4b163-1038">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4b163-1039">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4b163-1040">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-1041">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-1042">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-1043">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-1044">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="4b163-1044">Java Option</span></span> | <span data-ttu-id="4b163-1045">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1045">Default Value</span></span> | <span data-ttu-id="4b163-1046">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4b163-1047">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4b163-1048">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-1049">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-1050">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4b163-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4b163-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4b163-1052">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1052">Empty</span></span> | <span data-ttu-id="4b163-1053">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4b163-1054">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4b163-1055">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4b163-1056">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4b163-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4b163-1057">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4b163-1058">Opciones de serialización de JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4b163-1059">ASP.NET Core Signalr admite dos protocolos para codificar los mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4b163-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4b163-1060">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="4b163-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4b163-1061">La serialización de JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , que se puede agregar después `Startup.ConfigureServices` de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4b163-1062">El `AddJsonProtocol` método toma un delegado que recibe un `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="4b163-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4b163-1063">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese objeto es un objeto `JsonSerializerSettings` JSON.net que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="4b163-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4b163-1064">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="4b163-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="4b163-1065">Por ejemplo, para configurar el serializador de modo que use nombres de propiedad "PascalCase", en lugar de los nombres predeterminados de "camelCase", `Startup.ConfigureServices`use el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="4b163-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="4b163-1066">En el cliente de .NET, el `AddJsonProtocol` mismo método de extensión existe en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4b163-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4b163-1067">El `Microsoft.Extensions.DependencyInjection` espacio de nombres se debe importar para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4b163-1068">En este momento, no es posible configurar la serialización de JSON en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4b163-1069">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="4b163-1069">MessagePack serialization options</span></span>

<span data-ttu-id="4b163-1070">La serialización de MessagePack se puede configurar proporcionando un delegado a la llamada a [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4b163-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4b163-1071">Consulte [MessagePack en signalr](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="4b163-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-1072">En este momento, no es posible configurar la serialización de MessagePack en el cliente de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4b163-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4b163-1073">Configurar opciones de servidor</span><span class="sxs-lookup"><span data-stu-id="4b163-1073">Configure server options</span></span>

<span data-ttu-id="4b163-1074">En la tabla siguiente se describen las opciones para configurar los concentradores de Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4b163-1075">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1075">Option</span></span> | <span data-ttu-id="4b163-1076">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1076">Default Value</span></span> | <span data-ttu-id="4b163-1077">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="4b163-1078">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1078">15 seconds</span></span> | <span data-ttu-id="4b163-1079">Si el cliente no envía un mensaje de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="4b163-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4b163-1080">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-1081">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4b163-1082">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1082">15 seconds</span></span> | <span data-ttu-id="4b163-1083">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje ping para mantener abierta la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4b163-1084">Al cambiar `KeepAliveInterval`, cambie la `ServerTimeout` / `serverTimeoutInMilliseconds` configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4b163-1085">El valor `ServerTimeout` / `serverTimeoutInMilliseconds` recomendado es el doble `KeepAliveInterval` del valor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4b163-1086">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="4b163-1086">All installed protocols</span></span> | <span data-ttu-id="4b163-1087">Protocolos admitidos por este centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="4b163-1088">De forma predeterminada, todos los protocolos registrados en el servidor están permitidos, pero los protocolos se pueden quitar de esta lista para deshabilitar determinados protocolos para centros individuales.</span><span class="sxs-lookup"><span data-stu-id="4b163-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4b163-1089">Si `true`es, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método de concentrador.</span><span class="sxs-lookup"><span data-stu-id="4b163-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4b163-1090">El valor predeterminado `false`es, ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="4b163-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4b163-1091">Las opciones se pueden configurar para todos los concentradores proporcionando un delegado de `AddSignalR` opciones a `Startup.ConfigureServices`la llamada en.</span><span class="sxs-lookup"><span data-stu-id="4b163-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4b163-1092">Las opciones de un solo concentrador invalidan las `AddSignalR` opciones globales proporcionadas en <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="4b163-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4b163-1093">Opciones de configuración de HTTP avanzadas</span><span class="sxs-lookup"><span data-stu-id="4b163-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4b163-1094">Use `HttpConnectionDispatcherOptions` para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4b163-1095">Estas opciones se configuran pasando un delegado [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="4b163-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4b163-1096">En la tabla siguiente se describen las opciones para configurar las opciones de HTTP avanzadas de ASP.NET Core Signalr:</span><span class="sxs-lookup"><span data-stu-id="4b163-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4b163-1097">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1097">Option</span></span> | <span data-ttu-id="4b163-1098">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1098">Default Value</span></span> | <span data-ttu-id="4b163-1099">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4b163-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-1100">32 KB</span></span> | <span data-ttu-id="4b163-1101">Número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="4b163-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4b163-1102">Aumentar este valor permite que el servidor reciba mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4b163-1103">Los datos se recopilan `Authorize` automáticamente a partir de los atributos aplicados a la clase hub.</span><span class="sxs-lookup"><span data-stu-id="4b163-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4b163-1104">Una lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) que se usan para determinar si un cliente está autorizado para conectarse al centro.</span><span class="sxs-lookup"><span data-stu-id="4b163-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4b163-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="4b163-1105">32 KB</span></span> | <span data-ttu-id="4b163-1106">Número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="4b163-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4b163-1107">Al aumentar este valor, el servidor puede enviar mensajes de mayor tamaño, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="4b163-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4b163-1108">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="4b163-1108">All Transports are enabled.</span></span> | <span data-ttu-id="4b163-1109">Una enumeración de marcas `HttpTransportType` de bits que puede restringir los transportes que un cliente puede utilizar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="4b163-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4b163-1110">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1110">See below.</span></span> | <span data-ttu-id="4b163-1111">Opciones adicionales específicas del transporte de sondeo prolongado.</span><span class="sxs-lookup"><span data-stu-id="4b163-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4b163-1112">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1112">See below.</span></span> | <span data-ttu-id="4b163-1113">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4b163-1114">El transporte de sondeo largo tiene opciones adicionales que se pueden configurar mediante `LongPolling` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4b163-1115">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1115">Option</span></span> | <span data-ttu-id="4b163-1116">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1116">Default Value</span></span> | <span data-ttu-id="4b163-1117">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4b163-1118">90 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1118">90 seconds</span></span> | <span data-ttu-id="4b163-1119">Cantidad máxima de tiempo que el servidor espera a que se envíe un mensaje al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="4b163-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4b163-1120">Al disminuir este valor, el cliente emite nuevas solicitudes de sondeo con mayor frecuencia.</span><span class="sxs-lookup"><span data-stu-id="4b163-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4b163-1121">El transporte de WebSocket tiene opciones adicionales que se pueden configurar mediante `WebSockets` la propiedad:</span><span class="sxs-lookup"><span data-stu-id="4b163-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4b163-1122">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1122">Option</span></span> | <span data-ttu-id="4b163-1123">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1123">Default Value</span></span> | <span data-ttu-id="4b163-1124">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4b163-1125">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1125">5 seconds</span></span> | <span data-ttu-id="4b163-1126">Una vez que se cierra el servidor, si el cliente no se cierra dentro de este intervalo de tiempo, se finaliza la conexión.</span><span class="sxs-lookup"><span data-stu-id="4b163-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4b163-1127">Delegado que se puede utilizar para establecer el `Sec-WebSocket-Protocol` encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="4b163-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4b163-1128">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4b163-1129">Configuración de opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="4b163-1129">Configure client options</span></span>

<span data-ttu-id="4b163-1130">Las opciones de cliente se pueden configurar `HubConnectionBuilder` en el tipo (disponible en los clientes de .net y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4b163-1131">También está disponible en el cliente de Java, pero la `HttpHubConnectionBuilder` subclase es lo que contiene las opciones de configuración del generador, así como `HubConnection` en el propio.</span><span class="sxs-lookup"><span data-stu-id="4b163-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4b163-1132">registro</span><span class="sxs-lookup"><span data-stu-id="4b163-1132">Configure logging</span></span>

<span data-ttu-id="4b163-1133">El registro se configura en el cliente .NET mediante `ConfigureLogging` el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4b163-1134">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4b163-1135">Consulte la documentación de [Inicio de sesión ASP.net Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="4b163-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4b163-1136">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="4b163-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4b163-1137">Consulte la sección [proveedores de registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="4b163-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4b163-1138">Por ejemplo, para habilitar el registro de la consola `Microsoft.Extensions.Logging.Console` , instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="4b163-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4b163-1139">Llame al `AddConsole` método de extensión:</span><span class="sxs-lookup"><span data-stu-id="4b163-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4b163-1140">En el cliente de JavaScript, existe `configureLogging` un método similar.</span><span class="sxs-lookup"><span data-stu-id="4b163-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4b163-1141">Proporcione un `LogLevel` valor que indique el nivel mínimo de mensajes de registro que se va a producir.</span><span class="sxs-lookup"><span data-stu-id="4b163-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4b163-1142">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="4b163-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4b163-1143">Para deshabilitar el registro por `signalR.LogLevel.None` completo, `configureLogging` especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="4b163-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4b163-1144">Para obtener más información sobre el registro, consulte la documentación sobre el [diagnóstico de signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4b163-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4b163-1145">El cliente de Signalr Java usa la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="4b163-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4b163-1146">Se trata de una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="4b163-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4b163-1147">En el fragmento de código siguiente se muestra `java.util.logging` cómo usar con el cliente de signalr Java.</span><span class="sxs-lookup"><span data-stu-id="4b163-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4b163-1148">Si no configura el registro en las dependencias, SLF4J carga un registrador de no operaciones predeterminado con el siguiente mensaje de ADVERTENCIA:</span><span class="sxs-lookup"><span data-stu-id="4b163-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4b163-1149">Esto se puede omitir sin ningún riesgo.</span><span class="sxs-lookup"><span data-stu-id="4b163-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4b163-1150">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="4b163-1150">Configure allowed transports</span></span>

<span data-ttu-id="4b163-1151">Los transportes utilizados por Signalr se pueden configurar en la `WithUrl` llamada (`withUrl` en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4b163-1152">Una operación OR bit a bit de los `HttpTransportType` valores de se puede utilizar para restringir el cliente de modo que solo utilice los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="4b163-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4b163-1153">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="4b163-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="4b163-1154">Por ejemplo, para deshabilitar el transporte de eventos enviados por el servidor, pero permitir WebSockets y conexiones de sondeo largas:</span><span class="sxs-lookup"><span data-stu-id="4b163-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4b163-1155">En el cliente de JavaScript, los transportes se configuran estableciendo `transport` el campo en el objeto de opciones `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4b163-1156">Configurar la autenticación de portador</span><span class="sxs-lookup"><span data-stu-id="4b163-1156">Configure bearer authentication</span></span>

<span data-ttu-id="4b163-1157">Para proporcionar datos de autenticación junto con solicitudes de Signalr, `AccessTokenProvider` use la`accessTokenFactory` opción (en JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="4b163-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4b163-1158">En el cliente de .NET, este token de acceso se pasa como un token de "autenticación de portador" de HTTP `Authorization` (mediante el encabezado con `Bearer`un tipo de).</span><span class="sxs-lookup"><span data-stu-id="4b163-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4b163-1159">En el cliente de JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en los eventos enviados por el servidor y en las solicitudes de WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4b163-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4b163-1160">En estos casos, el token de acceso se proporciona como un valor `access_token`de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="4b163-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4b163-1161">En el cliente de .NET, `AccessTokenProvider` se puede especificar la opción mediante el delegado de `WithUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4b163-1162">En el cliente de JavaScript, el token de acceso se configura estableciendo `accessTokenFactory` el campo en el objeto de `withUrl`opciones en:</span><span class="sxs-lookup"><span data-stu-id="4b163-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4b163-1163">En el cliente de Signalr Java, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4b163-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4b163-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [RxJava](https://github.com/ReactiveX/RxJava) [sola\<cadena ](https://reactivex.io/documentation/single.html)de RxJava>.</span><span class="sxs-lookup"><span data-stu-id="4b163-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4b163-1165">Con una llamada a [Single. defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="4b163-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4b163-1166">Configurar las opciones de tiempo de espera y mantenimiento de conexión</span><span class="sxs-lookup"><span data-stu-id="4b163-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4b163-1167">En el propio objeto están disponibles opciones adicionales para configurar el `HubConnection` comportamiento de tiempo de espera y mantenimiento de conexión:</span><span class="sxs-lookup"><span data-stu-id="4b163-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-1169">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1169">Option</span></span> | <span data-ttu-id="4b163-1170">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1170">Default value</span></span> | <span data-ttu-id="4b163-1171">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4b163-1172">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-1173">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1173">Timeout for server activity.</span></span> <span data-ttu-id="4b163-1174">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-1175">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-1176">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4b163-1177">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1177">15 seconds</span></span> | <span data-ttu-id="4b163-1178">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-1179">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `Closed` el evento`onclose` (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4b163-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4b163-1180">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-1181">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="4b163-1182">En el cliente de .NET, los valores de tiempo `TimeSpan` de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="4b163-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4b163-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-1184">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1184">Option</span></span> | <span data-ttu-id="4b163-1185">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1185">Default value</span></span> | <span data-ttu-id="4b163-1186">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4b163-1187">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-1188">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1188">Timeout for server activity.</span></span> <span data-ttu-id="4b163-1189">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onclose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4b163-1190">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-1191">El valor recomendado es un número, como mínimo, el doble `KeepAliveInterval` del valor del servidor para dejar tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-1192">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-1193">Opción</span><span class="sxs-lookup"><span data-stu-id="4b163-1193">Option</span></span> | <span data-ttu-id="4b163-1194">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1194">Default value</span></span> | <span data-ttu-id="4b163-1195">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4b163-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4b163-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4b163-1197">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="4b163-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4b163-1198">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1198">Timeout for server activity.</span></span> <span data-ttu-id="4b163-1199">Si el servidor no ha enviado un mensaje en este intervalo, el cliente considera que el servidor está desconectado y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-1200">Este valor debe ser lo suficientemente grande como para que se envíe un mensaje ping desde el servidor **y** el cliente lo reciba dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="4b163-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4b163-1201">El valor recomendado es un número al menos el doble del valor `KeepAliveInterval` del servidor, para permitir que llegue el tiempo para que lleguen los ping.</span><span class="sxs-lookup"><span data-stu-id="4b163-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4b163-1202">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1202">15 seconds</span></span> | <span data-ttu-id="4b163-1203">Tiempo de espera para el protocolo de enlace inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="4b163-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="4b163-1204">Si el servidor no envía una respuesta de protocolo de enlace en este intervalo, el cliente cancela el protocolo de enlace y desencadena `onClose` el evento.</span><span class="sxs-lookup"><span data-stu-id="4b163-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4b163-1205">Se trata de una configuración avanzada que solo se debe modificar si se producen errores de tiempo de espera del Protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="4b163-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4b163-1206">Para más información sobre el proceso de enlace, consulte la [especificación del protocolo signalr Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4b163-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4b163-1207">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-1207">Configure additional options</span></span>

<span data-ttu-id="4b163-1208">Se pueden configurar opciones adicionales en el `WithUrl` método`withUrl` (en JavaScript) en `HubConnectionBuilder` o en las diversas API de configuración de `HttpHubConnectionBuilder` en el cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="4b163-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4b163-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="4b163-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4b163-1210">Opción de .NET</span><span class="sxs-lookup"><span data-stu-id="4b163-1210">.NET Option</span></span> |  <span data-ttu-id="4b163-1211">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1211">Default value</span></span> | <span data-ttu-id="4b163-1212">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4b163-1213">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4b163-1214">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-1215">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-1216">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4b163-1217">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1217">Empty</span></span> | <span data-ttu-id="4b163-1218">Colección de certificados TLS que se enviarán a las solicitudes de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4b163-1219">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1219">Empty</span></span> | <span data-ttu-id="4b163-1220">Colección de cookies HTTP que se enviarán con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4b163-1221">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1221">Empty</span></span> | <span data-ttu-id="4b163-1222">Credenciales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4b163-1223">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4b163-1223">5 seconds</span></span> | <span data-ttu-id="4b163-1224">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-1224">WebSockets only.</span></span> <span data-ttu-id="4b163-1225">Cantidad máxima de tiempo que el cliente espera después de cerrarse para que el servidor confirme la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="4b163-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4b163-1226">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4b163-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4b163-1227">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1227">Empty</span></span> | <span data-ttu-id="4b163-1228">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4b163-1229">Delegado que se puede utilizar para configurar o reemplazar el `HttpMessageHandler` utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4b163-1230">No se usa para las conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="4b163-1231">Este delegado debe devolver un valor distinto de NULL y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="4b163-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4b163-1232">Modifique la configuración de ese valor predeterminado y devuelva o devuelva una nueva `HttpMessageHandler` instancia.</span><span class="sxs-lookup"><span data-stu-id="4b163-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4b163-1233">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea conservar del controlador proporcionado; de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="4b163-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4b163-1234">Proxy HTTP que se va a usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4b163-1235">Establezca este valor booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4b163-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4b163-1236">Esto habilita el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="4b163-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4b163-1237">Delegado que se puede usar para configurar opciones adicionales de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4b163-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4b163-1238">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="4b163-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4b163-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4b163-1240">Opción de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4b163-1240">JavaScript Option</span></span> | <span data-ttu-id="4b163-1241">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1241">Default Value</span></span> | <span data-ttu-id="4b163-1242">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4b163-1243">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4b163-1244">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-1245">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-1246">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4b163-1247">Java</span><span class="sxs-lookup"><span data-stu-id="4b163-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="4b163-1248">Opción de Java</span><span class="sxs-lookup"><span data-stu-id="4b163-1248">Java Option</span></span> | <span data-ttu-id="4b163-1249">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="4b163-1249">Default Value</span></span> | <span data-ttu-id="4b163-1250">Descripción</span><span class="sxs-lookup"><span data-stu-id="4b163-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4b163-1251">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4b163-1252">Establézcalo en `true` para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="4b163-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4b163-1253">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4b163-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4b163-1254">Esta configuración no se puede habilitar cuando se usa el servicio Azure Signalr.</span><span class="sxs-lookup"><span data-stu-id="4b163-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4b163-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4b163-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4b163-1256">Vacío</span><span class="sxs-lookup"><span data-stu-id="4b163-1256">Empty</span></span> | <span data-ttu-id="4b163-1257">Asignación de encabezados HTTP adicionales que se van a enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b163-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4b163-1258">En el cliente de .NET, estas opciones se pueden modificar mediante el delegado de opciones `WithUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4b163-1259">En el cliente de JavaScript, estas opciones se pueden proporcionar en un objeto de JavaScript `withUrl`proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="4b163-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4b163-1260">En el cliente de Java, estas opciones se pueden configurar con los métodos del `HttpHubConnectionBuilder` devuelto desde`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4b163-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4b163-1261">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b163-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
