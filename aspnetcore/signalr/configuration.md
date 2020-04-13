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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223990"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="bcbb2-103">Configuración de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="bcbb2-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="bcbb2-104">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="bcbb2-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="bcbb2-105">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="bcbb2-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="bcbb2-107">La serialización JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="bcbb2-108">`AddJsonProtocol`se puede agregar después `Startup.ConfigureServices`de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en .</span><span class="sxs-lookup"><span data-stu-id="bcbb2-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bcbb2-109">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="bcbb2-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ese objeto es un objeto que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="bcbb2-111">Para obtener más información, consulte la [documentación de System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="bcbb2-112">Por ejemplo, para configurar el serializador para que no cambie la mayúsculas y minúsculas de `Startup.ConfigureServices`los nombres de propiedad, en lugar de los nombres predeterminados de "camelCase", utilice el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="bcbb2-113">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="bcbb2-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="bcbb2-115">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="bcbb2-116">Cambiar a Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="bcbb2-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="bcbb2-117">Si necesita características `Newtonsoft.Json` que no son `System.Text.Json`compatibles en , consulte [Cambiar a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="bcbb2-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="bcbb2-118">MessagePack serialization options</span></span>

<span data-ttu-id="bcbb2-119">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="bcbb2-120">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb2-121">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="bcbb2-122">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="bcbb2-122">Configure server options</span></span>

<span data-ttu-id="bcbb2-123">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="bcbb2-124">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-124">Option</span></span> | <span data-ttu-id="bcbb2-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-125">Default Value</span></span> | <span data-ttu-id="bcbb2-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="bcbb2-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-127">30 seconds</span></span> | <span data-ttu-id="bcbb2-128">El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="bcbb2-129">Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="bcbb2-130">El valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="bcbb2-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-131">15 seconds</span></span> | <span data-ttu-id="bcbb2-132">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="bcbb2-133">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-134">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="bcbb2-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-135">15 seconds</span></span> | <span data-ttu-id="bcbb2-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="bcbb2-137">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="bcbb2-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="bcbb2-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="bcbb2-139">All installed protocols</span></span> | <span data-ttu-id="bcbb2-140">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-140">Protocols supported by this hub.</span></span> <span data-ttu-id="bcbb2-141">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="bcbb2-142">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="bcbb2-143">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="bcbb2-144">El número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="bcbb2-145">Si se alcanza este límite, el procesamiento de invocaciones se bloquea hasta que el servidor procesa los elementos de secuencia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="bcbb2-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-146">32 KB</span></span> | <span data-ttu-id="bcbb2-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="bcbb2-148">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="bcbb2-149">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="bcbb2-150">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="bcbb2-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="bcbb2-151">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="bcbb2-152">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="bcbb2-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="bcbb2-153">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="bcbb2-154">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-154">Option</span></span> | <span data-ttu-id="bcbb2-155">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-155">Default Value</span></span> | <span data-ttu-id="bcbb2-156">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="bcbb2-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-157">32 KB</span></span> | <span data-ttu-id="bcbb2-158">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="bcbb2-159">Aumentar este valor permite al servidor recibir mensajes más grandes más rápidamente sin aplicar contrapresión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="bcbb2-160">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="bcbb2-161">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="bcbb2-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-162">32 KB</span></span> | <span data-ttu-id="bcbb2-163">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="bcbb2-164">Aumentar este valor permite al servidor almacenar en búfer mensajes más grandes más rápidamente sin esperar la contrapresión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="bcbb2-165">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-165">All Transports are enabled.</span></span> | <span data-ttu-id="bcbb2-166">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="bcbb2-167">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-167">See below.</span></span> | <span data-ttu-id="bcbb2-168">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="bcbb2-169">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-169">See below.</span></span> | <span data-ttu-id="bcbb2-170">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="bcbb2-171">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="bcbb2-172">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-172">Option</span></span> | <span data-ttu-id="bcbb2-173">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-173">Default Value</span></span> | <span data-ttu-id="bcbb2-174">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="bcbb2-175">90 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-175">90 seconds</span></span> | <span data-ttu-id="bcbb2-176">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="bcbb2-177">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="bcbb2-178">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="bcbb2-179">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-179">Option</span></span> | <span data-ttu-id="bcbb2-180">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-180">Default Value</span></span> | <span data-ttu-id="bcbb2-181">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="bcbb2-182">5 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-182">5 seconds</span></span> | <span data-ttu-id="bcbb2-183">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="bcbb2-184">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="bcbb2-185">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="bcbb2-186">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="bcbb2-186">Configure client options</span></span>

<span data-ttu-id="bcbb2-187">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="bcbb2-188">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="bcbb2-189">registro</span><span class="sxs-lookup"><span data-stu-id="bcbb2-189">Configure logging</span></span>

<span data-ttu-id="bcbb2-190">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="bcbb2-191">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="bcbb2-192">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb2-193">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="bcbb2-194">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="bcbb2-195">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="bcbb2-196">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="bcbb2-197">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="bcbb2-198">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="bcbb2-199">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="bcbb2-200">En lugar `LogLevel` de un valor, `string` también puede proporcionar un valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="bcbb2-201">Esto es útil al configurar el registro de SignalR en `LogLevel` entornos donde no tiene acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="bcbb2-202">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-202">The following table lists the available log levels.</span></span> <span data-ttu-id="bcbb2-203">El valor que `configureLogging` proporcione para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="bcbb2-204">Los mensajes registrados en este nivel, **o los niveles enumerados después de él en la tabla,** se registrarán.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="bcbb2-205">String</span><span class="sxs-lookup"><span data-stu-id="bcbb2-205">String</span></span>                      | <span data-ttu-id="bcbb2-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="bcbb2-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="bcbb2-207">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="bcbb2-208">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="bcbb2-209">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="bcbb2-210">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="bcbb2-211">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="bcbb2-212">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="bcbb2-213">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="bcbb2-214">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="bcbb2-215">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="bcbb2-216">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-216">Configure allowed transports</span></span>

<span data-ttu-id="bcbb2-217">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="bcbb2-218">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="bcbb2-219">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-219">All transports are enabled by default.</span></span>

<span data-ttu-id="bcbb2-220">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="bcbb2-221">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="bcbb2-222">En esta versión de los websockets del cliente Java es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="bcbb2-223">En el cliente Java, el `withTransport` transporte se `HttpHubConnectionBuilder`selecciona con el método en el archivo .</span><span class="sxs-lookup"><span data-stu-id="bcbb2-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="bcbb2-224">El cliente Java utiliza de forma predeterminada el transporte webSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="bcbb2-225">El cliente Java de SignalR todavía no admite la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="bcbb2-226">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="bcbb2-226">Configure bearer authentication</span></span>

<span data-ttu-id="bcbb2-227">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="bcbb2-228">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="bcbb2-229">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="bcbb2-230">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="bcbb2-231">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="bcbb2-232">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="bcbb2-233">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="bcbb2-234">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="bcbb2-235">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="bcbb2-236">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="bcbb2-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="bcbb2-237">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="bcbb2-238">.NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="bcbb2-239">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-239">Option</span></span> | <span data-ttu-id="bcbb2-240">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-240">Default value</span></span> | <span data-ttu-id="bcbb2-241">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="bcbb2-242">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-243">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-243">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-244">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="bcbb2-245">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-246">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="bcbb2-247">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-247">15 seconds</span></span> | <span data-ttu-id="bcbb2-248">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="bcbb2-249">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="bcbb2-250">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-251">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="bcbb2-252">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-252">15 seconds</span></span> | <span data-ttu-id="bcbb2-253">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="bcbb2-254">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="bcbb2-255">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="bcbb2-256">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="bcbb2-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="bcbb2-258">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-258">Option</span></span> | <span data-ttu-id="bcbb2-259">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-259">Default value</span></span> | <span data-ttu-id="bcbb2-260">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="bcbb2-261">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-262">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-262">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-263">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="bcbb2-264">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-265">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="bcbb2-266">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-267">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="bcbb2-268">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="bcbb2-269">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="bcbb2-270">Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-270">Java</span></span>](#tab/java)

| <span data-ttu-id="bcbb2-271">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-271">Option</span></span> | <span data-ttu-id="bcbb2-272">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-272">Default value</span></span> | <span data-ttu-id="bcbb2-273">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="bcbb2-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="bcbb2-275">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-276">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-276">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-277">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="bcbb2-278">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-279">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="bcbb2-280">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-280">15 seconds</span></span> | <span data-ttu-id="bcbb2-281">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="bcbb2-282">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="bcbb2-283">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-284">Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="bcbb2-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="bcbb2-286">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-287">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="bcbb2-288">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="bcbb2-289">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="bcbb2-290">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="bcbb2-290">Configure additional options</span></span>

<span data-ttu-id="bcbb2-291">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="bcbb2-292">.NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="bcbb2-293">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-293">.NET Option</span></span> |  <span data-ttu-id="bcbb2-294">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-294">Default value</span></span> | <span data-ttu-id="bcbb2-295">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="bcbb2-296">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="bcbb2-297">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-298">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-299">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="bcbb2-300">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-300">Empty</span></span> | <span data-ttu-id="bcbb2-301">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="bcbb2-302">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-302">Empty</span></span> | <span data-ttu-id="bcbb2-303">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="bcbb2-304">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-304">Empty</span></span> | <span data-ttu-id="bcbb2-305">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="bcbb2-306">5 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-306">5 seconds</span></span> | <span data-ttu-id="bcbb2-307">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-307">WebSockets only.</span></span> <span data-ttu-id="bcbb2-308">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="bcbb2-309">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="bcbb2-310">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-310">Empty</span></span> | <span data-ttu-id="bcbb2-311">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="bcbb2-312">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="bcbb2-313">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="bcbb2-314">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="bcbb2-315">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="bcbb2-316">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="bcbb2-317">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="bcbb2-318">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="bcbb2-319">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="bcbb2-320">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="bcbb2-321">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="bcbb2-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="bcbb2-323">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-323">JavaScript Option</span></span> | <span data-ttu-id="bcbb2-324">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-324">Default Value</span></span> | <span data-ttu-id="bcbb2-325">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="bcbb2-326">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="bcbb2-327">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-328">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-329">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="bcbb2-330">Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-330">Java</span></span>](#tab/java)

| <span data-ttu-id="bcbb2-331">Opción Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-331">Java Option</span></span> | <span data-ttu-id="bcbb2-332">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-332">Default Value</span></span> | <span data-ttu-id="bcbb2-333">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="bcbb2-334">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="bcbb2-335">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-336">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-337">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="bcbb2-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="bcbb2-339">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-339">Empty</span></span> | <span data-ttu-id="bcbb2-340">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="bcbb2-341">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="bcbb2-342">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="bcbb2-343">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="bcbb2-344">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bcbb2-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="bcbb2-345">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="bcbb2-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="bcbb2-346">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="bcbb2-347">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="bcbb2-348">La serialización JSON se puede configurar en el servidor mediante el método de `Startup.ConfigureServices` extensión [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bcbb2-349">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="bcbb2-350">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese `JsonSerializerSettings` objeto es un objeto JSON.NET que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="bcbb2-351">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="bcbb2-352">Por ejemplo, para configurar el serializador para que utilice los nombres de propiedad "PascalCase", `Startup.ConfigureServices`en lugar de los nombres predeterminados "camelCase", utilice el siguiente código en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="bcbb2-353">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="bcbb2-354">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="bcbb2-355">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="bcbb2-356">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="bcbb2-356">MessagePack serialization options</span></span>

<span data-ttu-id="bcbb2-357">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="bcbb2-358">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb2-359">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="bcbb2-360">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="bcbb2-360">Configure server options</span></span>

<span data-ttu-id="bcbb2-361">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="bcbb2-362">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-362">Option</span></span> | <span data-ttu-id="bcbb2-363">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-363">Default Value</span></span> | <span data-ttu-id="bcbb2-364">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="bcbb2-365">30 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-365">30 seconds</span></span> | <span data-ttu-id="bcbb2-366">El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="bcbb2-367">Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="bcbb2-368">El valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="bcbb2-369">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-369">15 seconds</span></span> | <span data-ttu-id="bcbb2-370">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="bcbb2-371">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-372">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="bcbb2-373">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-373">15 seconds</span></span> | <span data-ttu-id="bcbb2-374">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="bcbb2-375">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="bcbb2-376">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="bcbb2-377">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="bcbb2-377">All installed protocols</span></span> | <span data-ttu-id="bcbb2-378">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-378">Protocols supported by this hub.</span></span> <span data-ttu-id="bcbb2-379">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="bcbb2-380">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="bcbb2-381">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="bcbb2-382">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="bcbb2-383">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="bcbb2-384">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="bcbb2-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="bcbb2-385">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="bcbb2-386">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="bcbb2-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="bcbb2-387">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="bcbb2-388">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-388">Option</span></span> | <span data-ttu-id="bcbb2-389">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-389">Default Value</span></span> | <span data-ttu-id="bcbb2-390">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="bcbb2-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-391">32 KB</span></span> | <span data-ttu-id="bcbb2-392">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="bcbb2-393">Aumentar este valor permite al servidor recibir mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="bcbb2-394">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="bcbb2-395">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="bcbb2-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-396">32 KB</span></span> | <span data-ttu-id="bcbb2-397">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="bcbb2-398">Aumentar este valor permite al servidor enviar mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="bcbb2-399">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-399">All Transports are enabled.</span></span> | <span data-ttu-id="bcbb2-400">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="bcbb2-401">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-401">See below.</span></span> | <span data-ttu-id="bcbb2-402">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="bcbb2-403">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-403">See below.</span></span> | <span data-ttu-id="bcbb2-404">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="bcbb2-405">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="bcbb2-406">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-406">Option</span></span> | <span data-ttu-id="bcbb2-407">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-407">Default Value</span></span> | <span data-ttu-id="bcbb2-408">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="bcbb2-409">90 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-409">90 seconds</span></span> | <span data-ttu-id="bcbb2-410">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="bcbb2-411">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="bcbb2-412">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="bcbb2-413">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-413">Option</span></span> | <span data-ttu-id="bcbb2-414">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-414">Default Value</span></span> | <span data-ttu-id="bcbb2-415">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="bcbb2-416">5 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-416">5 seconds</span></span> | <span data-ttu-id="bcbb2-417">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="bcbb2-418">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="bcbb2-419">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="bcbb2-420">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="bcbb2-420">Configure client options</span></span>

<span data-ttu-id="bcbb2-421">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="bcbb2-422">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="bcbb2-423">registro</span><span class="sxs-lookup"><span data-stu-id="bcbb2-423">Configure logging</span></span>

<span data-ttu-id="bcbb2-424">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="bcbb2-425">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="bcbb2-426">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb2-427">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="bcbb2-428">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="bcbb2-429">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="bcbb2-430">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="bcbb2-431">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="bcbb2-432">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="bcbb2-433">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="bcbb2-434">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="bcbb2-435">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="bcbb2-436">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="bcbb2-437">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="bcbb2-438">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="bcbb2-439">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="bcbb2-440">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="bcbb2-441">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-441">Configure allowed transports</span></span>

<span data-ttu-id="bcbb2-442">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="bcbb2-443">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="bcbb2-444">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-444">All transports are enabled by default.</span></span>

<span data-ttu-id="bcbb2-445">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="bcbb2-446">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="bcbb2-447">En esta versión de los websockets del cliente Java es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="bcbb2-448">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="bcbb2-448">Configure bearer authentication</span></span>

<span data-ttu-id="bcbb2-449">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="bcbb2-450">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="bcbb2-451">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="bcbb2-452">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="bcbb2-453">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="bcbb2-454">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="bcbb2-455">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="bcbb2-456">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="bcbb2-457">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="bcbb2-458">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="bcbb2-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="bcbb2-459">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="bcbb2-460">.NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="bcbb2-461">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-461">Option</span></span> | <span data-ttu-id="bcbb2-462">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-462">Default value</span></span> | <span data-ttu-id="bcbb2-463">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="bcbb2-464">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-465">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-465">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-466">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="bcbb2-467">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-468">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="bcbb2-469">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-469">15 seconds</span></span> | <span data-ttu-id="bcbb2-470">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="bcbb2-471">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="bcbb2-472">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-473">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="bcbb2-474">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-474">15 seconds</span></span> | <span data-ttu-id="bcbb2-475">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="bcbb2-476">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="bcbb2-477">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="bcbb2-478">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="bcbb2-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="bcbb2-480">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-480">Option</span></span> | <span data-ttu-id="bcbb2-481">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-481">Default value</span></span> | <span data-ttu-id="bcbb2-482">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="bcbb2-483">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-484">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-484">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-485">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="bcbb2-486">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-487">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="bcbb2-488">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-489">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="bcbb2-490">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="bcbb2-491">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="bcbb2-492">Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-492">Java</span></span>](#tab/java)

| <span data-ttu-id="bcbb2-493">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-493">Option</span></span> | <span data-ttu-id="bcbb2-494">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-494">Default value</span></span> | <span data-ttu-id="bcbb2-495">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="bcbb2-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="bcbb2-497">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-498">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-498">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-499">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="bcbb2-500">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-501">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="bcbb2-502">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-502">15 seconds</span></span> | <span data-ttu-id="bcbb2-503">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="bcbb2-504">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="bcbb2-505">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-506">Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="bcbb2-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="bcbb2-508">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-509">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="bcbb2-510">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="bcbb2-511">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="bcbb2-512">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="bcbb2-512">Configure additional options</span></span>

<span data-ttu-id="bcbb2-513">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="bcbb2-514">.NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="bcbb2-515">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-515">.NET Option</span></span> |  <span data-ttu-id="bcbb2-516">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-516">Default value</span></span> | <span data-ttu-id="bcbb2-517">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="bcbb2-518">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="bcbb2-519">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-520">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-521">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="bcbb2-522">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-522">Empty</span></span> | <span data-ttu-id="bcbb2-523">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="bcbb2-524">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-524">Empty</span></span> | <span data-ttu-id="bcbb2-525">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="bcbb2-526">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-526">Empty</span></span> | <span data-ttu-id="bcbb2-527">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="bcbb2-528">5 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-528">5 seconds</span></span> | <span data-ttu-id="bcbb2-529">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-529">WebSockets only.</span></span> <span data-ttu-id="bcbb2-530">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="bcbb2-531">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="bcbb2-532">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-532">Empty</span></span> | <span data-ttu-id="bcbb2-533">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="bcbb2-534">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="bcbb2-535">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="bcbb2-536">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="bcbb2-537">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="bcbb2-538">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="bcbb2-539">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="bcbb2-540">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="bcbb2-541">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="bcbb2-542">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="bcbb2-543">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="bcbb2-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="bcbb2-545">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-545">JavaScript Option</span></span> | <span data-ttu-id="bcbb2-546">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-546">Default Value</span></span> | <span data-ttu-id="bcbb2-547">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="bcbb2-548">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="bcbb2-549">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-550">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-551">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="bcbb2-552">Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-552">Java</span></span>](#tab/java)

| <span data-ttu-id="bcbb2-553">Opción Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-553">Java Option</span></span> | <span data-ttu-id="bcbb2-554">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-554">Default Value</span></span> | <span data-ttu-id="bcbb2-555">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="bcbb2-556">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="bcbb2-557">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-558">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-559">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="bcbb2-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="bcbb2-561">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-561">Empty</span></span> | <span data-ttu-id="bcbb2-562">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="bcbb2-563">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="bcbb2-564">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="bcbb2-565">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="bcbb2-566">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bcbb2-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="bcbb2-567">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="bcbb2-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="bcbb2-568">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="bcbb2-569">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="bcbb2-570">La serialización JSON se puede configurar en el servidor mediante el método de `Startup.ConfigureServices` extensión [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="bcbb2-571">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="bcbb2-572">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese `JsonSerializerSettings` objeto es un objeto JSON.NET que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="bcbb2-573">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="bcbb2-574">Por ejemplo, para configurar el serializador para que utilice los nombres de propiedad "PascalCase", `Startup.ConfigureServices`en lugar de los nombres predeterminados "camelCase", utilice el siguiente código en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="bcbb2-575">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="bcbb2-576">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="bcbb2-577">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="bcbb2-578">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="bcbb2-578">MessagePack serialization options</span></span>

<span data-ttu-id="bcbb2-579">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="bcbb2-580">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb2-581">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="bcbb2-582">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="bcbb2-582">Configure server options</span></span>

<span data-ttu-id="bcbb2-583">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="bcbb2-584">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-584">Option</span></span> | <span data-ttu-id="bcbb2-585">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-585">Default Value</span></span> | <span data-ttu-id="bcbb2-586">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="bcbb2-587">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-587">15 seconds</span></span> | <span data-ttu-id="bcbb2-588">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="bcbb2-589">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-590">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="bcbb2-591">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-591">15 seconds</span></span> | <span data-ttu-id="bcbb2-592">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="bcbb2-593">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="bcbb2-594">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="bcbb2-595">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="bcbb2-595">All installed protocols</span></span> | <span data-ttu-id="bcbb2-596">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-596">Protocols supported by this hub.</span></span> <span data-ttu-id="bcbb2-597">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="bcbb2-598">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="bcbb2-599">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="bcbb2-600">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="bcbb2-601">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="bcbb2-602">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="bcbb2-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="bcbb2-603">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="bcbb2-604">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="bcbb2-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="bcbb2-605">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="bcbb2-606">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-606">Option</span></span> | <span data-ttu-id="bcbb2-607">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-607">Default Value</span></span> | <span data-ttu-id="bcbb2-608">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="bcbb2-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-609">32 KB</span></span> | <span data-ttu-id="bcbb2-610">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="bcbb2-611">Aumentar este valor permite al servidor recibir mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="bcbb2-612">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="bcbb2-613">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="bcbb2-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="bcbb2-614">32 KB</span></span> | <span data-ttu-id="bcbb2-615">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="bcbb2-616">Aumentar este valor permite al servidor enviar mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="bcbb2-617">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-617">All Transports are enabled.</span></span> | <span data-ttu-id="bcbb2-618">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="bcbb2-619">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-619">See below.</span></span> | <span data-ttu-id="bcbb2-620">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="bcbb2-621">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-621">See below.</span></span> | <span data-ttu-id="bcbb2-622">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="bcbb2-623">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="bcbb2-624">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-624">Option</span></span> | <span data-ttu-id="bcbb2-625">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-625">Default Value</span></span> | <span data-ttu-id="bcbb2-626">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="bcbb2-627">90 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-627">90 seconds</span></span> | <span data-ttu-id="bcbb2-628">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="bcbb2-629">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="bcbb2-630">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="bcbb2-631">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-631">Option</span></span> | <span data-ttu-id="bcbb2-632">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-632">Default Value</span></span> | <span data-ttu-id="bcbb2-633">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="bcbb2-634">5 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-634">5 seconds</span></span> | <span data-ttu-id="bcbb2-635">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="bcbb2-636">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="bcbb2-637">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="bcbb2-638">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="bcbb2-638">Configure client options</span></span>

<span data-ttu-id="bcbb2-639">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="bcbb2-640">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="bcbb2-641">registro</span><span class="sxs-lookup"><span data-stu-id="bcbb2-641">Configure logging</span></span>

<span data-ttu-id="bcbb2-642">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="bcbb2-643">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="bcbb2-644">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="bcbb2-645">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="bcbb2-646">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="bcbb2-647">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="bcbb2-648">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="bcbb2-649">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="bcbb2-650">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="bcbb2-651">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="bcbb2-652">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="bcbb2-653">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="bcbb2-654">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="bcbb2-655">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="bcbb2-656">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="bcbb2-657">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="bcbb2-658">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="bcbb2-659">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-659">Configure allowed transports</span></span>

<span data-ttu-id="bcbb2-660">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="bcbb2-661">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="bcbb2-662">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-662">All transports are enabled by default.</span></span>

<span data-ttu-id="bcbb2-663">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="bcbb2-664">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="bcbb2-665">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="bcbb2-665">Configure bearer authentication</span></span>

<span data-ttu-id="bcbb2-666">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="bcbb2-667">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="bcbb2-668">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="bcbb2-669">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="bcbb2-670">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="bcbb2-671">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="bcbb2-672">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="bcbb2-673">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="bcbb2-674">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="bcbb2-675">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="bcbb2-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="bcbb2-676">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="bcbb2-677">.NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="bcbb2-678">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-678">Option</span></span> | <span data-ttu-id="bcbb2-679">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-679">Default value</span></span> | <span data-ttu-id="bcbb2-680">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="bcbb2-681">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-682">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-682">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-683">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="bcbb2-684">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-685">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="bcbb2-686">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-686">15 seconds</span></span> | <span data-ttu-id="bcbb2-687">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="bcbb2-688">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="bcbb2-689">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-690">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="bcbb2-691">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="bcbb2-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="bcbb2-693">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-693">Option</span></span> | <span data-ttu-id="bcbb2-694">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-694">Default value</span></span> | <span data-ttu-id="bcbb2-695">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="bcbb2-696">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-697">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-697">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-698">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="bcbb2-699">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-700">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="bcbb2-701">Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-701">Java</span></span>](#tab/java)

| <span data-ttu-id="bcbb2-702">Opción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-702">Option</span></span> | <span data-ttu-id="bcbb2-703">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-703">Default value</span></span> | <span data-ttu-id="bcbb2-704">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="bcbb2-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="bcbb2-706">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="bcbb2-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="bcbb2-707">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-707">Timeout for server activity.</span></span> <span data-ttu-id="bcbb2-708">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="bcbb2-709">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="bcbb2-710">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor, para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="bcbb2-711">15 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-711">15 seconds</span></span> | <span data-ttu-id="bcbb2-712">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="bcbb2-713">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="bcbb2-714">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="bcbb2-715">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="bcbb2-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="bcbb2-716">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="bcbb2-716">Configure additional options</span></span>

<span data-ttu-id="bcbb2-717">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="bcbb2-718">.NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="bcbb2-719">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="bcbb2-719">.NET Option</span></span> |  <span data-ttu-id="bcbb2-720">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-720">Default value</span></span> | <span data-ttu-id="bcbb2-721">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="bcbb2-722">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="bcbb2-723">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-724">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-725">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="bcbb2-726">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-726">Empty</span></span> | <span data-ttu-id="bcbb2-727">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="bcbb2-728">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-728">Empty</span></span> | <span data-ttu-id="bcbb2-729">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="bcbb2-730">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-730">Empty</span></span> | <span data-ttu-id="bcbb2-731">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="bcbb2-732">5 segundos</span><span class="sxs-lookup"><span data-stu-id="bcbb2-732">5 seconds</span></span> | <span data-ttu-id="bcbb2-733">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-733">WebSockets only.</span></span> <span data-ttu-id="bcbb2-734">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="bcbb2-735">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="bcbb2-736">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-736">Empty</span></span> | <span data-ttu-id="bcbb2-737">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="bcbb2-738">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="bcbb2-739">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="bcbb2-740">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="bcbb2-741">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="bcbb2-742">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="bcbb2-743">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="bcbb2-744">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="bcbb2-745">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="bcbb2-746">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="bcbb2-747">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="bcbb2-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="bcbb2-749">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="bcbb2-749">JavaScript Option</span></span> | <span data-ttu-id="bcbb2-750">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-750">Default Value</span></span> | <span data-ttu-id="bcbb2-751">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="bcbb2-752">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="bcbb2-753">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-754">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-755">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="bcbb2-756">Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-756">Java</span></span>](#tab/java)

| <span data-ttu-id="bcbb2-757">Opción Java</span><span class="sxs-lookup"><span data-stu-id="bcbb2-757">Java Option</span></span> | <span data-ttu-id="bcbb2-758">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="bcbb2-758">Default Value</span></span> | <span data-ttu-id="bcbb2-759">Descripción</span><span class="sxs-lookup"><span data-stu-id="bcbb2-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="bcbb2-760">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="bcbb2-761">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="bcbb2-762">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="bcbb2-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="bcbb2-763">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="bcbb2-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="bcbb2-765">Vacío</span><span class="sxs-lookup"><span data-stu-id="bcbb2-765">Empty</span></span> | <span data-ttu-id="bcbb2-766">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="bcbb2-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="bcbb2-767">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="bcbb2-768">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="bcbb2-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="bcbb2-769">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="bcbb2-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="bcbb2-770">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="bcbb2-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
