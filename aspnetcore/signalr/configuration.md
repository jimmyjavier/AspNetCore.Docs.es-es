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
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="d5a3e-103">Configuración de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d5a3e-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5a3e-104">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-105">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5a3e-106">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5a3e-107">La serialización JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5a3e-108">`AddJsonProtocol`se puede agregar después `Startup.ConfigureServices`de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5a3e-109">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5a3e-110">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ese objeto es un objeto que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5a3e-111">Para obtener más información, consulte la [documentación de System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5a3e-112">Por ejemplo, para configurar el serializador para que no cambie la mayúsculas y minúsculas de `Startup.ConfigureServices`los nombres de propiedad, en lugar de los nombres predeterminados de "camelCase", utilice el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d5a3e-113">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5a3e-114">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5a3e-115">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5a3e-116">Cambiar a Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="d5a3e-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5a3e-117">Si necesita características `Newtonsoft.Json` que no son `System.Text.Json`compatibles en , consulte [Cambiar a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5a3e-118">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-118">MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-119">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5a3e-120">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-121">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5a3e-122">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="d5a3e-122">Configure server options</span></span>

<span data-ttu-id="d5a3e-123">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5a3e-124">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-124">Option</span></span> | <span data-ttu-id="d5a3e-125">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-125">Default Value</span></span> | <span data-ttu-id="d5a3e-126">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5a3e-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-127">30 seconds</span></span> | <span data-ttu-id="d5a3e-128">El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5a3e-129">Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5a3e-130">El valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-131">15 seconds</span></span> | <span data-ttu-id="d5a3e-132">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5a3e-133">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-134">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-135">15 seconds</span></span> | <span data-ttu-id="d5a3e-136">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5a3e-137">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5a3e-138">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5a3e-139">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5a3e-139">All installed protocols</span></span> | <span data-ttu-id="d5a3e-140">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d5a3e-141">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5a3e-142">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5a3e-143">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5a3e-144">El número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5a3e-145">Si se alcanza este límite, el procesamiento de invocaciones se bloquea hasta que el servidor procesa los elementos de secuencia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5a3e-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-146">32 KB</span></span> | <span data-ttu-id="d5a3e-147">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d5a3e-148">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5a3e-149">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5a3e-150">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="d5a3e-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5a3e-151">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5a3e-152">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5a3e-153">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5a3e-154">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-154">Option</span></span> | <span data-ttu-id="d5a3e-155">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-155">Default Value</span></span> | <span data-ttu-id="d5a3e-156">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5a3e-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-157">32 KB</span></span> | <span data-ttu-id="d5a3e-158">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5a3e-159">Aumentar este valor permite al servidor recibir mensajes más grandes más rápidamente sin aplicar contrapresión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5a3e-160">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5a3e-161">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5a3e-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-162">32 KB</span></span> | <span data-ttu-id="d5a3e-163">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5a3e-164">Aumentar este valor permite al servidor almacenar en búfer mensajes más grandes más rápidamente sin esperar la contrapresión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5a3e-165">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-165">All Transports are enabled.</span></span> | <span data-ttu-id="d5a3e-166">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5a3e-167">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-167">See below.</span></span> | <span data-ttu-id="d5a3e-168">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5a3e-169">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-169">See below.</span></span> | <span data-ttu-id="d5a3e-170">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d5a3e-171">0</span><span class="sxs-lookup"><span data-stu-id="d5a3e-171">0</span></span> | <span data-ttu-id="d5a3e-172">Especifique la versión mínima del protocolo de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d5a3e-173">Esto se utiliza para limitar los clientes a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d5a3e-174">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5a3e-175">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-175">Option</span></span> | <span data-ttu-id="d5a3e-176">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-176">Default Value</span></span> | <span data-ttu-id="d5a3e-177">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5a3e-178">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-178">90 seconds</span></span> | <span data-ttu-id="d5a3e-179">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5a3e-180">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5a3e-181">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5a3e-182">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-182">Option</span></span> | <span data-ttu-id="d5a3e-183">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-183">Default Value</span></span> | <span data-ttu-id="d5a3e-184">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5a3e-185">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-185">5 seconds</span></span> | <span data-ttu-id="d5a3e-186">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5a3e-187">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5a3e-188">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5a3e-189">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="d5a3e-189">Configure client options</span></span>

<span data-ttu-id="d5a3e-190">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5a3e-191">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5a3e-192">registro</span><span class="sxs-lookup"><span data-stu-id="d5a3e-192">Configure logging</span></span>

<span data-ttu-id="d5a3e-193">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5a3e-194">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5a3e-195">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-196">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5a3e-197">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5a3e-198">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5a3e-199">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5a3e-200">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5a3e-201">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5a3e-202">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5a3e-203">En lugar `LogLevel` de un valor, `string` también puede proporcionar un valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5a3e-204">Esto es útil al configurar el registro de SignalR en `LogLevel` entornos donde no tiene acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5a3e-205">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-205">The following table lists the available log levels.</span></span> <span data-ttu-id="d5a3e-206">El valor que `configureLogging` proporcione para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5a3e-207">Los mensajes registrados en este nivel, **o los niveles enumerados después de él en la tabla,** se registrarán.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5a3e-208">String</span><span class="sxs-lookup"><span data-stu-id="d5a3e-208">String</span></span>                      | <span data-ttu-id="d5a3e-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5a3e-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5a3e-210">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5a3e-211">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5a3e-212">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5a3e-213">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5a3e-214">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5a3e-215">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5a3e-216">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5a3e-217">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5a3e-218">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5a3e-219">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-219">Configure allowed transports</span></span>

<span data-ttu-id="d5a3e-220">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5a3e-221">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5a3e-222">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-222">All transports are enabled by default.</span></span>

<span data-ttu-id="d5a3e-223">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5a3e-224">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5a3e-225">En esta versión de los websockets del cliente Java es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5a3e-226">En el cliente Java, el `withTransport` transporte se `HttpHubConnectionBuilder`selecciona con el método en el archivo .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5a3e-227">El cliente Java utiliza de forma predeterminada el transporte webSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5a3e-228">El cliente Java de SignalR todavía no admite la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5a3e-229">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="d5a3e-229">Configure bearer authentication</span></span>

<span data-ttu-id="d5a3e-230">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5a3e-231">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5a3e-232">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5a3e-233">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5a3e-234">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5a3e-235">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5a3e-236">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5a3e-237">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5a3e-238">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5a3e-239">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="d5a3e-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5a3e-240">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-241">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-242">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-242">Option</span></span> | <span data-ttu-id="d5a3e-243">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-243">Default value</span></span> | <span data-ttu-id="d5a3e-244">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5a3e-245">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-246">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-246">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-247">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-248">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-249">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-250">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-250">15 seconds</span></span> | <span data-ttu-id="d5a3e-251">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-252">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-253">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-254">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-255">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-255">15 seconds</span></span> | <span data-ttu-id="d5a3e-256">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-257">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-258">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5a3e-259">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-261">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-261">Option</span></span> | <span data-ttu-id="d5a3e-262">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-262">Default value</span></span> | <span data-ttu-id="d5a3e-263">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5a3e-264">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-265">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-265">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-266">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5a3e-267">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-268">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5a3e-269">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-270">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-271">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-272">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-273">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-273">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-274">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-274">Option</span></span> | <span data-ttu-id="d5a3e-275">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-275">Default value</span></span> | <span data-ttu-id="d5a3e-276">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5a3e-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5a3e-278">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-279">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-279">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-280">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-281">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-282">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5a3e-283">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-283">15 seconds</span></span> | <span data-ttu-id="d5a3e-284">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-285">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-286">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-287">Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5a3e-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5a3e-289">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-290">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-291">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-292">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5a3e-293">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-293">Configure additional options</span></span>

<span data-ttu-id="d5a3e-294">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-295">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-296">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-296">.NET Option</span></span> |  <span data-ttu-id="d5a3e-297">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-297">Default value</span></span> | <span data-ttu-id="d5a3e-298">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-299">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5a3e-300">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-301">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-302">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5a3e-303">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-303">Empty</span></span> | <span data-ttu-id="d5a3e-304">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5a3e-305">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-305">Empty</span></span> | <span data-ttu-id="d5a3e-306">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5a3e-307">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-307">Empty</span></span> | <span data-ttu-id="d5a3e-308">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5a3e-309">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-309">5 seconds</span></span> | <span data-ttu-id="d5a3e-310">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-310">WebSockets only.</span></span> <span data-ttu-id="d5a3e-311">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5a3e-312">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5a3e-313">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-313">Empty</span></span> | <span data-ttu-id="d5a3e-314">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5a3e-315">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5a3e-316">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5a3e-317">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5a3e-318">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5a3e-319">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5a3e-320">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5a3e-321">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5a3e-322">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5a3e-323">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5a3e-324">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-326">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-326">JavaScript Option</span></span> | <span data-ttu-id="d5a3e-327">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-327">Default Value</span></span> | <span data-ttu-id="d5a3e-328">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5a3e-329">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5a3e-330">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-331">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-332">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-333">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-333">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-334">Opción Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-334">Java Option</span></span> | <span data-ttu-id="d5a3e-335">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-335">Default Value</span></span> | <span data-ttu-id="d5a3e-336">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-337">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5a3e-338">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-339">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-340">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5a3e-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5a3e-342">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-342">Empty</span></span> | <span data-ttu-id="d5a3e-343">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5a3e-344">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5a3e-345">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5a3e-346">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5a3e-347">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5a3e-348">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-349">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5a3e-350">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5a3e-351">La serialización JSON se puede configurar en el servidor mediante el método de extensión [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5a3e-352">`AddJsonProtocol`se puede agregar después `Startup.ConfigureServices`de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5a3e-353">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5a3e-354">La propiedad [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) de `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> ese objeto es un objeto que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5a3e-355">Para obtener más información, consulte la [documentación de System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5a3e-356">Por ejemplo, para configurar el serializador para que no cambie la mayúsculas y minúsculas de `Startup.ConfigureServices`los nombres de propiedad, en lugar de los nombres predeterminados de "camelCase", utilice el código siguiente en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="d5a3e-357">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5a3e-358">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5a3e-359">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5a3e-360">Cambiar a Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="d5a3e-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5a3e-361">Si necesita características `Newtonsoft.Json` que no son `System.Text.Json`compatibles en , consulte [Cambiar a Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5a3e-362">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-362">MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-363">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5a3e-364">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-365">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5a3e-366">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="d5a3e-366">Configure server options</span></span>

<span data-ttu-id="d5a3e-367">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5a3e-368">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-368">Option</span></span> | <span data-ttu-id="d5a3e-369">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-369">Default Value</span></span> | <span data-ttu-id="d5a3e-370">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5a3e-371">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-371">30 seconds</span></span> | <span data-ttu-id="d5a3e-372">El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5a3e-373">Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5a3e-374">El valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-375">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-375">15 seconds</span></span> | <span data-ttu-id="d5a3e-376">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5a3e-377">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-378">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-379">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-379">15 seconds</span></span> | <span data-ttu-id="d5a3e-380">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5a3e-381">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5a3e-382">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5a3e-383">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5a3e-383">All installed protocols</span></span> | <span data-ttu-id="d5a3e-384">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-384">Protocols supported by this hub.</span></span> <span data-ttu-id="d5a3e-385">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5a3e-386">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5a3e-387">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5a3e-388">El número máximo de elementos que se pueden almacenar en búfer para las secuencias de carga de cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5a3e-389">Si se alcanza este límite, el procesamiento de invocaciones se bloquea hasta que el servidor procesa los elementos de secuencia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5a3e-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-390">32 KB</span></span> | <span data-ttu-id="d5a3e-391">Tamaño máximo de un único mensaje de concentrador entrante.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d5a3e-392">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5a3e-393">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5a3e-394">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="d5a3e-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5a3e-395">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5a3e-396">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5a3e-397">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5a3e-398">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-398">Option</span></span> | <span data-ttu-id="d5a3e-399">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-399">Default Value</span></span> | <span data-ttu-id="d5a3e-400">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5a3e-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-401">32 KB</span></span> | <span data-ttu-id="d5a3e-402">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer antes de aplicar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5a3e-403">Aumentar este valor permite al servidor recibir mensajes más grandes más rápidamente sin aplicar contrapresión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5a3e-404">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5a3e-405">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5a3e-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-406">32 KB</span></span> | <span data-ttu-id="d5a3e-407">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer antes de observar la contrapresión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5a3e-408">Aumentar este valor permite al servidor almacenar en búfer mensajes más grandes más rápidamente sin esperar la contrapresión, pero puede aumentar el consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5a3e-409">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-409">All Transports are enabled.</span></span> | <span data-ttu-id="d5a3e-410">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5a3e-411">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-411">See below.</span></span> | <span data-ttu-id="d5a3e-412">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5a3e-413">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-413">See below.</span></span> | <span data-ttu-id="d5a3e-414">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5a3e-415">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5a3e-416">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-416">Option</span></span> | <span data-ttu-id="d5a3e-417">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-417">Default Value</span></span> | <span data-ttu-id="d5a3e-418">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5a3e-419">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-419">90 seconds</span></span> | <span data-ttu-id="d5a3e-420">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5a3e-421">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5a3e-422">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5a3e-423">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-423">Option</span></span> | <span data-ttu-id="d5a3e-424">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-424">Default Value</span></span> | <span data-ttu-id="d5a3e-425">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5a3e-426">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-426">5 seconds</span></span> | <span data-ttu-id="d5a3e-427">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5a3e-428">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5a3e-429">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5a3e-430">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="d5a3e-430">Configure client options</span></span>

<span data-ttu-id="d5a3e-431">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5a3e-432">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5a3e-433">registro</span><span class="sxs-lookup"><span data-stu-id="d5a3e-433">Configure logging</span></span>

<span data-ttu-id="d5a3e-434">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5a3e-435">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5a3e-436">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-437">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5a3e-438">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5a3e-439">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5a3e-440">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5a3e-441">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5a3e-442">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5a3e-443">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5a3e-444">En lugar `LogLevel` de un valor, `string` también puede proporcionar un valor que represente un nombre de nivel de registro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5a3e-445">Esto es útil al configurar el registro de SignalR en `LogLevel` entornos donde no tiene acceso a las constantes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5a3e-446">En la tabla siguiente se enumeran los niveles de registro disponibles.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-446">The following table lists the available log levels.</span></span> <span data-ttu-id="d5a3e-447">El valor que `configureLogging` proporcione para establecer el nivel de registro **mínimo** que se registrará.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5a3e-448">Los mensajes registrados en este nivel, **o los niveles enumerados después de él en la tabla,** se registrarán.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5a3e-449">String</span><span class="sxs-lookup"><span data-stu-id="d5a3e-449">String</span></span>                      | <span data-ttu-id="d5a3e-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5a3e-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5a3e-451">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5a3e-452">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5a3e-453">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5a3e-454">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5a3e-455">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5a3e-456">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5a3e-457">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5a3e-458">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5a3e-459">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5a3e-460">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-460">Configure allowed transports</span></span>

<span data-ttu-id="d5a3e-461">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5a3e-462">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5a3e-463">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-463">All transports are enabled by default.</span></span>

<span data-ttu-id="d5a3e-464">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5a3e-465">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5a3e-466">En esta versión de los websockets del cliente Java es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5a3e-467">En el cliente Java, el `withTransport` transporte se `HttpHubConnectionBuilder`selecciona con el método en el archivo .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5a3e-468">El cliente Java utiliza de forma predeterminada el transporte webSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5a3e-469">El cliente Java de SignalR todavía no admite la reserva de transporte.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5a3e-470">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="d5a3e-470">Configure bearer authentication</span></span>

<span data-ttu-id="d5a3e-471">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5a3e-472">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5a3e-473">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5a3e-474">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5a3e-475">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5a3e-476">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5a3e-477">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5a3e-478">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5a3e-479">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5a3e-480">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="d5a3e-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5a3e-481">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-482">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-483">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-483">Option</span></span> | <span data-ttu-id="d5a3e-484">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-484">Default value</span></span> | <span data-ttu-id="d5a3e-485">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5a3e-486">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-487">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-487">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-488">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-489">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-490">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-491">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-491">15 seconds</span></span> | <span data-ttu-id="d5a3e-492">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-493">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-494">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-495">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-496">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-496">15 seconds</span></span> | <span data-ttu-id="d5a3e-497">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-498">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-499">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5a3e-500">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-502">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-502">Option</span></span> | <span data-ttu-id="d5a3e-503">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-503">Default value</span></span> | <span data-ttu-id="d5a3e-504">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5a3e-505">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-506">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-506">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-507">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5a3e-508">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-509">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5a3e-510">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-511">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-512">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-513">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-514">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-514">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-515">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-515">Option</span></span> | <span data-ttu-id="d5a3e-516">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-516">Default value</span></span> | <span data-ttu-id="d5a3e-517">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5a3e-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5a3e-519">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-520">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-520">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-521">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-522">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-523">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5a3e-524">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-524">15 seconds</span></span> | <span data-ttu-id="d5a3e-525">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-526">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-527">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-528">Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5a3e-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5a3e-530">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-531">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-532">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-533">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5a3e-534">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-534">Configure additional options</span></span>

<span data-ttu-id="d5a3e-535">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-536">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-537">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-537">.NET Option</span></span> |  <span data-ttu-id="d5a3e-538">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-538">Default value</span></span> | <span data-ttu-id="d5a3e-539">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-540">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5a3e-541">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-542">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-543">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5a3e-544">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-544">Empty</span></span> | <span data-ttu-id="d5a3e-545">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5a3e-546">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-546">Empty</span></span> | <span data-ttu-id="d5a3e-547">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5a3e-548">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-548">Empty</span></span> | <span data-ttu-id="d5a3e-549">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5a3e-550">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-550">5 seconds</span></span> | <span data-ttu-id="d5a3e-551">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-551">WebSockets only.</span></span> <span data-ttu-id="d5a3e-552">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5a3e-553">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5a3e-554">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-554">Empty</span></span> | <span data-ttu-id="d5a3e-555">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5a3e-556">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5a3e-557">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5a3e-558">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5a3e-559">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5a3e-560">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5a3e-561">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5a3e-562">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5a3e-563">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5a3e-564">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5a3e-565">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-567">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-567">JavaScript Option</span></span> | <span data-ttu-id="d5a3e-568">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-568">Default Value</span></span> | <span data-ttu-id="d5a3e-569">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5a3e-570">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5a3e-571">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-572">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-573">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-574">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-574">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-575">Opción Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-575">Java Option</span></span> | <span data-ttu-id="d5a3e-576">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-576">Default Value</span></span> | <span data-ttu-id="d5a3e-577">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-578">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5a3e-579">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-580">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-581">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5a3e-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5a3e-583">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-583">Empty</span></span> | <span data-ttu-id="d5a3e-584">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5a3e-585">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5a3e-586">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5a3e-587">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5a3e-588">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5a3e-589">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-590">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5a3e-591">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5a3e-592">La serialización JSON se puede configurar en el servidor mediante el método de `Startup.ConfigureServices` extensión [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5a3e-593">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5a3e-594">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese `JsonSerializerSettings` objeto es un objeto JSON.NET que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5a3e-595">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d5a3e-596">Por ejemplo, para configurar el serializador para que utilice los nombres de propiedad "PascalCase", `Startup.ConfigureServices`en lugar de los nombres predeterminados "camelCase", utilice el siguiente código en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d5a3e-597">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5a3e-598">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5a3e-599">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5a3e-600">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-600">MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-601">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5a3e-602">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-603">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5a3e-604">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="d5a3e-604">Configure server options</span></span>

<span data-ttu-id="d5a3e-605">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5a3e-606">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-606">Option</span></span> | <span data-ttu-id="d5a3e-607">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-607">Default Value</span></span> | <span data-ttu-id="d5a3e-608">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5a3e-609">30 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-609">30 seconds</span></span> | <span data-ttu-id="d5a3e-610">El servidor considerará que el cliente está desconectado si no ha recibido un mensaje (incluido keep-alive) en este intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5a3e-611">Podría tomar más tiempo que este intervalo de tiempo de espera para que el cliente se marque realmente desconectado, debido a cómo se implementa esto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5a3e-612">El valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-613">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-613">15 seconds</span></span> | <span data-ttu-id="d5a3e-614">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5a3e-615">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-616">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-617">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-617">15 seconds</span></span> | <span data-ttu-id="d5a3e-618">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5a3e-619">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5a3e-620">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5a3e-621">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5a3e-621">All installed protocols</span></span> | <span data-ttu-id="d5a3e-622">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-622">Protocols supported by this hub.</span></span> <span data-ttu-id="d5a3e-623">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5a3e-624">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5a3e-625">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d5a3e-626">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5a3e-627">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5a3e-628">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="d5a3e-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5a3e-629">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5a3e-630">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5a3e-631">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5a3e-632">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-632">Option</span></span> | <span data-ttu-id="d5a3e-633">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-633">Default Value</span></span> | <span data-ttu-id="d5a3e-634">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5a3e-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-635">32 KB</span></span> | <span data-ttu-id="d5a3e-636">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d5a3e-637">Aumentar este valor permite al servidor recibir mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5a3e-638">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5a3e-639">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5a3e-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-640">32 KB</span></span> | <span data-ttu-id="d5a3e-641">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d5a3e-642">Aumentar este valor permite al servidor enviar mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5a3e-643">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-643">All Transports are enabled.</span></span> | <span data-ttu-id="d5a3e-644">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5a3e-645">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-645">See below.</span></span> | <span data-ttu-id="d5a3e-646">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5a3e-647">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-647">See below.</span></span> | <span data-ttu-id="d5a3e-648">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5a3e-649">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5a3e-650">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-650">Option</span></span> | <span data-ttu-id="d5a3e-651">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-651">Default Value</span></span> | <span data-ttu-id="d5a3e-652">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5a3e-653">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-653">90 seconds</span></span> | <span data-ttu-id="d5a3e-654">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5a3e-655">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5a3e-656">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5a3e-657">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-657">Option</span></span> | <span data-ttu-id="d5a3e-658">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-658">Default Value</span></span> | <span data-ttu-id="d5a3e-659">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5a3e-660">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-660">5 seconds</span></span> | <span data-ttu-id="d5a3e-661">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5a3e-662">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5a3e-663">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5a3e-664">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="d5a3e-664">Configure client options</span></span>

<span data-ttu-id="d5a3e-665">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5a3e-666">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5a3e-667">registro</span><span class="sxs-lookup"><span data-stu-id="d5a3e-667">Configure logging</span></span>

<span data-ttu-id="d5a3e-668">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5a3e-669">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5a3e-670">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-671">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5a3e-672">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5a3e-673">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5a3e-674">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5a3e-675">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5a3e-676">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5a3e-677">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5a3e-678">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5a3e-679">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5a3e-680">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5a3e-681">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5a3e-682">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5a3e-683">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5a3e-684">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5a3e-685">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-685">Configure allowed transports</span></span>

<span data-ttu-id="d5a3e-686">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5a3e-687">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5a3e-688">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-688">All transports are enabled by default.</span></span>

<span data-ttu-id="d5a3e-689">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5a3e-690">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5a3e-691">En esta versión de los websockets del cliente Java es el único transporte disponible.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5a3e-692">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="d5a3e-692">Configure bearer authentication</span></span>

<span data-ttu-id="d5a3e-693">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5a3e-694">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5a3e-695">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5a3e-696">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5a3e-697">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5a3e-698">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5a3e-699">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5a3e-700">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5a3e-701">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5a3e-702">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="d5a3e-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5a3e-703">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-704">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-705">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-705">Option</span></span> | <span data-ttu-id="d5a3e-706">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-706">Default value</span></span> | <span data-ttu-id="d5a3e-707">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5a3e-708">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-709">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-709">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-710">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-711">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-712">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-713">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-713">15 seconds</span></span> | <span data-ttu-id="d5a3e-714">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-715">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-716">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-717">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-718">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-718">15 seconds</span></span> | <span data-ttu-id="d5a3e-719">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-720">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-721">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5a3e-722">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-724">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-724">Option</span></span> | <span data-ttu-id="d5a3e-725">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-725">Default value</span></span> | <span data-ttu-id="d5a3e-726">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5a3e-727">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-728">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-728">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-729">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5a3e-730">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-731">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5a3e-732">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-733">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-734">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-735">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-736">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-736">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-737">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-737">Option</span></span> | <span data-ttu-id="d5a3e-738">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-738">Default value</span></span> | <span data-ttu-id="d5a3e-739">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5a3e-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5a3e-741">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-742">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-742">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-743">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-744">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-745">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5a3e-746">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-746">15 seconds</span></span> | <span data-ttu-id="d5a3e-747">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-748">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-749">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-750">Para obtener más detalles sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5a3e-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5a3e-752">15 segundos (15.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-753">Determina el intervalo en el que el cliente envía mensajes ping.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5a3e-754">El envío de cualquier mensaje desde el cliente restablece el temporizador al inicio del intervalo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5a3e-755">Si el cliente no ha enviado `ClientTimeoutInterval` un mensaje en el conjunto en el servidor, el servidor considera que el cliente está desconectado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5a3e-756">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-756">Configure additional options</span></span>

<span data-ttu-id="d5a3e-757">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-758">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-759">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-759">.NET Option</span></span> |  <span data-ttu-id="d5a3e-760">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-760">Default value</span></span> | <span data-ttu-id="d5a3e-761">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-762">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5a3e-763">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-764">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-765">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5a3e-766">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-766">Empty</span></span> | <span data-ttu-id="d5a3e-767">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5a3e-768">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-768">Empty</span></span> | <span data-ttu-id="d5a3e-769">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5a3e-770">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-770">Empty</span></span> | <span data-ttu-id="d5a3e-771">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5a3e-772">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-772">5 seconds</span></span> | <span data-ttu-id="d5a3e-773">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-773">WebSockets only.</span></span> <span data-ttu-id="d5a3e-774">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5a3e-775">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5a3e-776">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-776">Empty</span></span> | <span data-ttu-id="d5a3e-777">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5a3e-778">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5a3e-779">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5a3e-780">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5a3e-781">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5a3e-782">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5a3e-783">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5a3e-784">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5a3e-785">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5a3e-786">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5a3e-787">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-789">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-789">JavaScript Option</span></span> | <span data-ttu-id="d5a3e-790">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-790">Default Value</span></span> | <span data-ttu-id="d5a3e-791">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5a3e-792">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5a3e-793">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-794">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-795">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-796">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-796">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-797">Opción Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-797">Java Option</span></span> | <span data-ttu-id="d5a3e-798">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-798">Default Value</span></span> | <span data-ttu-id="d5a3e-799">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-800">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5a3e-801">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-802">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-803">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5a3e-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5a3e-805">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-805">Empty</span></span> | <span data-ttu-id="d5a3e-806">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5a3e-807">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5a3e-808">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5a3e-809">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5a3e-810">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5a3e-811">Opciones de serialización JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-812">ASP.NET Core SignalR admite dos protocolos para codificar mensajes: [JSON](https://www.json.org/) y [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5a3e-813">Cada protocolo tiene opciones de configuración de serialización.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5a3e-814">La serialización JSON se puede configurar en el servidor mediante el método de `Startup.ConfigureServices` extensión [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) que se puede agregar después de [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) en el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5a3e-815">El `AddJsonProtocol` método toma un delegado `options` que recibe un objeto.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5a3e-816">La propiedad [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) de ese `JsonSerializerSettings` objeto es un objeto JSON.NET que se puede usar para configurar la serialización de argumentos y valores devueltos.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5a3e-817">Para más información, vea la [documentación de JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d5a3e-818">Por ejemplo, para configurar el serializador para que utilice los nombres de propiedad "PascalCase", `Startup.ConfigureServices`en lugar de los nombres predeterminados "camelCase", utilice el siguiente código en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d5a3e-819">En el cliente .NET, existe el mismo `AddJsonProtocol` método de extensión en [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5a3e-820">El `Microsoft.Extensions.DependencyInjection` espacio de nombres debe importarse para resolver el método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5a3e-821">No es posible configurar la serialización JSON en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5a3e-822">Opciones de serialización de MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5a3e-822">MessagePack serialization options</span></span>

<span data-ttu-id="d5a3e-823">MessagePack serialización se puede configurar proporcionando un delegado a la [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) llamada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5a3e-824">Consulte [MessagePack en SignalR](xref:signalr/messagepackhubprotocol) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-825">No es posible configurar la serialización de MessagePack en el cliente JavaScript en este momento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5a3e-826">Configurar las opciones del servidor</span><span class="sxs-lookup"><span data-stu-id="d5a3e-826">Configure server options</span></span>

<span data-ttu-id="d5a3e-827">En la tabla siguiente se describen las opciones para configurar los concentradores de SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5a3e-828">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-828">Option</span></span> | <span data-ttu-id="d5a3e-829">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-829">Default Value</span></span> | <span data-ttu-id="d5a3e-830">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-831">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-831">15 seconds</span></span> | <span data-ttu-id="d5a3e-832">Si el cliente no envía un mensaje de protocolo de enlace inicial dentro de este intervalo de tiempo, la conexión se cierra.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5a3e-833">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-834">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5a3e-835">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-835">15 seconds</span></span> | <span data-ttu-id="d5a3e-836">Si el servidor no ha enviado un mensaje dentro de este intervalo, se envía automáticamente un mensaje de ping para mantener la conexión abierta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5a3e-837">Al `KeepAliveInterval`cambiar , `ServerTimeout` / `serverTimeoutInMilliseconds` cambie la configuración en el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5a3e-838">El `ServerTimeout` / `serverTimeoutInMilliseconds` valor recomendado `KeepAliveInterval` es el doble del valor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5a3e-839">Todos los protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="d5a3e-839">All installed protocols</span></span> | <span data-ttu-id="d5a3e-840">Protocolos admitidos por este concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-840">Protocols supported by this hub.</span></span> <span data-ttu-id="d5a3e-841">De forma predeterminada, se permiten todos los protocolos registrados en el servidor, pero los protocolos se pueden quitar de esta lista para deshabilitar protocolos específicos para concentradores individuales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5a3e-842">Si `true`, los mensajes de excepción detallados se devuelven a los clientes cuando se produce una excepción en un método Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5a3e-843">El valor `false`predeterminado es , ya que estos mensajes de excepción pueden contener información confidencial.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d5a3e-844">Las opciones se pueden configurar para todos los `AddSignalR` concentradores proporcionando un delegado de opciones a la llamada en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5a3e-845">Las opciones para un único concentrador `AddSignalR` anulan las <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opciones globales proporcionadas y se pueden configurar mediante:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5a3e-846">Opciones avanzadas de configuración HTTP</span><span class="sxs-lookup"><span data-stu-id="d5a3e-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5a3e-847">Se `HttpConnectionDispatcherOptions` utiliza para configurar opciones avanzadas relacionadas con los transportes y la administración del búfer de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5a3e-848">Estas opciones se configuran pasando un delegado `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) en .</span><span class="sxs-lookup"><span data-stu-id="d5a3e-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5a3e-849">En la tabla siguiente se describen las opciones para configurar las opciones HTTP avanzadas de ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5a3e-850">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-850">Option</span></span> | <span data-ttu-id="d5a3e-851">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-851">Default Value</span></span> | <span data-ttu-id="d5a3e-852">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5a3e-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-853">32 KB</span></span> | <span data-ttu-id="d5a3e-854">El número máximo de bytes recibidos del cliente que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d5a3e-855">Aumentar este valor permite al servidor recibir mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5a3e-856">Datos recopilados automáticamente `Authorize` de los atributos aplicados a la clase Hub.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5a3e-857">Una lista de [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos utilizados para determinar si un cliente está autorizado para conectarse al concentrador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5a3e-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5a3e-858">32 KB</span></span> | <span data-ttu-id="d5a3e-859">El número máximo de bytes enviados por la aplicación que el servidor almacena en búfer.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d5a3e-860">Aumentar este valor permite al servidor enviar mensajes más grandes, pero puede afectar negativamente al consumo de memoria.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5a3e-861">Todos los transportes están habilitados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-861">All Transports are enabled.</span></span> | <span data-ttu-id="d5a3e-862">Un bit marca `HttpTransportType` la enumeración de valores que pueden restringir los transportes que un cliente puede usar para conectarse.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5a3e-863">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-863">See below.</span></span> | <span data-ttu-id="d5a3e-864">Opciones adicionales específicas para el transporte de sondeo largo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5a3e-865">Véase a continuación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-865">See below.</span></span> | <span data-ttu-id="d5a3e-866">Opciones adicionales específicas del transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5a3e-867">El transporte de sondeo largo tiene opciones `LongPolling` adicionales que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5a3e-868">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-868">Option</span></span> | <span data-ttu-id="d5a3e-869">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-869">Default Value</span></span> | <span data-ttu-id="d5a3e-870">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5a3e-871">90 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-871">90 seconds</span></span> | <span data-ttu-id="d5a3e-872">La cantidad máxima de tiempo que el servidor espera a que un mensaje envíe al cliente antes de finalizar una única solicitud de sondeo.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5a3e-873">La reducción de este valor hace que el cliente emita nuevas solicitudes de sondeo con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5a3e-874">El transporte WebSocket tiene opciones adicionales `WebSockets` que se pueden configurar mediante la propiedad:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5a3e-875">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-875">Option</span></span> | <span data-ttu-id="d5a3e-876">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-876">Default Value</span></span> | <span data-ttu-id="d5a3e-877">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5a3e-878">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-878">5 seconds</span></span> | <span data-ttu-id="d5a3e-879">Después de que se cierre el servidor, si el cliente no puede cerrarse dentro de este intervalo de tiempo, se termina la conexión.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5a3e-880">Delegado que se puede usar `Sec-WebSocket-Protocol` para establecer el encabezado en un valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5a3e-881">El delegado recibe los valores solicitados por el cliente como entrada y se espera que devuelva el valor deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5a3e-882">Configurar las opciones de cliente</span><span class="sxs-lookup"><span data-stu-id="d5a3e-882">Configure client options</span></span>

<span data-ttu-id="d5a3e-883">Las opciones de cliente `HubConnectionBuilder` se pueden configurar en el tipo (disponible en los clientes .NET y JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5a3e-884">También está disponible en el cliente `HttpHubConnectionBuilder` Java, pero la subclase es lo que `HubConnection` contiene las opciones de configuración del constructor, así como en el propio.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5a3e-885">registro</span><span class="sxs-lookup"><span data-stu-id="d5a3e-885">Configure logging</span></span>

<span data-ttu-id="d5a3e-886">El registro se configura en `ConfigureLogging` el cliente .NET mediante el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5a3e-887">Los proveedores de registro y los filtros se pueden registrar de la misma manera que en el servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5a3e-888">Consulte la documentación [de Inicio de sesión en ASP.NET Core](xref:fundamentals/logging/index) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5a3e-889">Para registrar los proveedores de registro, debe instalar los paquetes necesarios.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5a3e-890">Consulte la sección Proveedores de [registro integrados](xref:fundamentals/logging/index#built-in-logging-providers) de los documentos para obtener una lista completa.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5a3e-891">Por ejemplo, para habilitar el `Microsoft.Extensions.Logging.Console` registro de consola, instale el paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5a3e-892">Llame `AddConsole` al método de extensión:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5a3e-893">En el cliente JavaScript, existe un método similar. `configureLogging`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5a3e-894">Proporcione `LogLevel` un valor que indique el nivel mínimo de mensajes de registro que se producirán.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5a3e-895">Los registros se escriben en la ventana de la consola del explorador.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5a3e-896">Para deshabilitar el registro `signalR.LogLevel.None` por `configureLogging` completo, especifique en el método.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5a3e-897">Para obtener más información sobre el registro, consulte la documentación de Diagnóstico de [SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5a3e-898">El cliente Java de SignalR utiliza la biblioteca [SLF4J](https://www.slf4j.org/) para el registro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5a3e-899">Es una API de registro de alto nivel que permite a los usuarios de la biblioteca elegir su propia implementación de registro específica mediante la incorporación de una dependencia de registro específica.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5a3e-900">El siguiente fragmento de `java.util.logging` código muestra cómo utilizar con el cliente Java de SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5a3e-901">Si no configura el registro en las dependencias, SLF4J carga un registrador predeterminado sin operación con el siguiente mensaje de advertencia:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5a3e-902">Esto se puede ignorar de forma segura.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5a3e-903">Configurar los transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-903">Configure allowed transports</span></span>

<span data-ttu-id="d5a3e-904">Los transportes utilizados por SignalR `WithUrl` se`withUrl` pueden configurar en la llamada (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5a3e-905">Un OR bit a bit `HttpTransportType` de los valores de se puede utilizar para restringir el cliente para utilizar sólo los transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5a3e-906">Todos los transportes están habilitados de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-906">All transports are enabled by default.</span></span>

<span data-ttu-id="d5a3e-907">Por ejemplo, para deshabilitar el transporte de eventos enviados por servidor, pero permitir webSockets y conexiones de sondeo largo:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5a3e-908">En el cliente JavaScript, los transportes se configuran estableciendo el `transport` campo en el objeto options proporcionado en: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5a3e-909">Configurar la autenticación del portador</span><span class="sxs-lookup"><span data-stu-id="d5a3e-909">Configure bearer authentication</span></span>

<span data-ttu-id="d5a3e-910">Para proporcionar datos de autenticación junto con `AccessTokenProvider` solicitudes de SignalR, use la opción (en`accessTokenFactory` JavaScript) para especificar una función que devuelva el token de acceso deseado.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5a3e-911">En el cliente .NET, este token de acceso se pasa como `Authorization` un token HTTP `Bearer`"Autenticación de portador" (mediante el encabezado con un tipo de ).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5a3e-912">En el cliente JavaScript, el token de acceso se usa como un token de portador, **excepto** en algunos casos en los que las API del explorador restringen la capacidad de aplicar encabezados (específicamente, en eventos enviados por el servidor y solicitudes WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5a3e-913">En estos casos, el token de acceso `access_token`se proporciona como un valor de cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5a3e-914">En el cliente .NET, la `AccessTokenProvider` opción se puede `WithUrl`especificar mediante el delegado de opciones en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5a3e-915">En el cliente JavaScript, el token `accessTokenFactory` de acceso se `withUrl`configura estableciendo el campo en el objeto options en:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5a3e-916">En el cliente Java de SignalR, puede configurar un token de portador que se usará para la autenticación proporcionando un generador de tokens de acceso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5a3e-917">Utilícelo [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para proporcionar una [cadena única\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5a3e-918">Con una llamada a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), puede escribir lógica para generar tokens de acceso para el cliente.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5a3e-919">Configurar el tiempo de espera y mantener las opciones de mantener en vivo</span><span class="sxs-lookup"><span data-stu-id="d5a3e-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5a3e-920">Las opciones adicionales para configurar el tiempo de `HubConnection` espera y el comportamiento de mantenimiento están disponibles en el propio objeto:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-921">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-922">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-922">Option</span></span> | <span data-ttu-id="d5a3e-923">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-923">Default value</span></span> | <span data-ttu-id="d5a3e-924">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5a3e-925">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-926">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-926">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-927">Si el servidor no ha enviado un mensaje en este intervalo, el `Closed` cliente`onclose` considera el servidor desconectado y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-928">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-929">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5a3e-930">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-930">15 seconds</span></span> | <span data-ttu-id="d5a3e-931">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-932">Si el servidor no envía una respuesta de protocolo de enlace en este `Closed` intervalo,`onclose` el cliente cancela el protocolo de enlace y desencadena el evento (en JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5a3e-933">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-934">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d5a3e-935">En el cliente .NET, los `TimeSpan` valores de tiempo de espera se especifican como valores.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-937">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-937">Option</span></span> | <span data-ttu-id="d5a3e-938">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-938">Default value</span></span> | <span data-ttu-id="d5a3e-939">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5a3e-940">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-941">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-941">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-942">Si el servidor no ha enviado un mensaje en este intervalo, el `onclose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5a3e-943">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-944">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-945">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-945">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-946">Opción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-946">Option</span></span> | <span data-ttu-id="d5a3e-947">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-947">Default value</span></span> | <span data-ttu-id="d5a3e-948">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5a3e-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5a3e-950">30 segundos (30.000 milisegundos)</span><span class="sxs-lookup"><span data-stu-id="d5a3e-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5a3e-951">Tiempo de espera para la actividad del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-951">Timeout for server activity.</span></span> <span data-ttu-id="d5a3e-952">Si el servidor no ha enviado un mensaje en este intervalo, el `onClose` cliente considera el servidor desconectado y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-953">Este valor debe ser lo suficientemente grande como para que un mensaje de ping sea enviado desde el servidor **y** recibido por el cliente dentro del intervalo de tiempo de espera.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5a3e-954">El valor recomendado es un número al `KeepAliveInterval` menos el doble del valor del servidor, para permitir el tiempo para que lleguen los pings.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5a3e-955">15 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-955">15 seconds</span></span> | <span data-ttu-id="d5a3e-956">Tiempo de espera para el apretón de manos inicial del servidor.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5a3e-957">Si el servidor no envía una respuesta de protocolo de enlace en este `onClose` intervalo, el cliente cancela el protocolo de enlace y desencadena el evento.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5a3e-958">Esta es una configuración avanzada que solo debe modificarse si se producen errores de tiempo de espera de protocolo de enlace debido a una latencia de red grave.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5a3e-959">Para obtener más información sobre el proceso de apretón de manos, consulte la especificación del protocolo de concentrador de [SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5a3e-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5a3e-960">Configuración de opciones adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-960">Configure additional options</span></span>

<span data-ttu-id="d5a3e-961">Las opciones adicionales se `WithUrl` `withUrl` pueden configurar en `HubConnectionBuilder` el método (en JavaScript) en o en las diversas API de configuración `HttpHubConnectionBuilder` en el cliente Java:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5a3e-962">.NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5a3e-963">Opción .NET</span><span class="sxs-lookup"><span data-stu-id="d5a3e-963">.NET Option</span></span> |  <span data-ttu-id="d5a3e-964">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-964">Default value</span></span> | <span data-ttu-id="d5a3e-965">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-966">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5a3e-967">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-968">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-969">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5a3e-970">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-970">Empty</span></span> | <span data-ttu-id="d5a3e-971">Colección de certificados TLS para enviar para autenticar solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5a3e-972">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-972">Empty</span></span> | <span data-ttu-id="d5a3e-973">Una colección de cookies HTTP para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5a3e-974">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-974">Empty</span></span> | <span data-ttu-id="d5a3e-975">Credenciales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5a3e-976">5 segundos</span><span class="sxs-lookup"><span data-stu-id="d5a3e-976">5 seconds</span></span> | <span data-ttu-id="d5a3e-977">Sólo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-977">WebSockets only.</span></span> <span data-ttu-id="d5a3e-978">La cantidad máxima de tiempo que el cliente espera después del cierre para que el servidor reconozca la solicitud de cierre.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5a3e-979">Si el servidor no reconoce el cierre dentro de este tiempo, el cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5a3e-980">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-980">Empty</span></span> | <span data-ttu-id="d5a3e-981">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5a3e-982">Un delegado que se puede utilizar `HttpMessageHandler` para configurar o reemplazar el utilizado para enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5a3e-983">No se utiliza para conexiones WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5a3e-984">Este delegado debe devolver un valor distinto de null y recibe el valor predeterminado como parámetro.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5a3e-985">Modifique la configuración de ese valor predeterminado y `HttpMessageHandler` devuélvalo o devuelva una nueva instancia.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5a3e-986">**Al reemplazar el controlador, asegúrese de copiar la configuración que desea mantener del controlador proporcionado, de lo contrario, las opciones configuradas (como cookies y encabezados) no se aplicarán al nuevo controlador.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5a3e-987">Un proxy HTTP para usar al enviar solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5a3e-988">Establezca este booleano para enviar las credenciales predeterminadas para las solicitudes HTTP y WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5a3e-989">Esto permite el uso de la autenticación de Windows.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5a3e-990">Delegado que se puede usar para configurar opciones de WebSocket adicionales.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5a3e-991">Recibe una instancia de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que se puede usar para configurar las opciones.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5a3e-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5a3e-993">Opción JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5a3e-993">JavaScript Option</span></span> | <span data-ttu-id="d5a3e-994">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-994">Default Value</span></span> | <span data-ttu-id="d5a3e-995">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5a3e-996">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5a3e-997">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-998">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-999">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5a3e-1000">Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="d5a3e-1001">Opción Java</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1001">Java Option</span></span> | <span data-ttu-id="d5a3e-1002">Valor predeterminado</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1002">Default Value</span></span> | <span data-ttu-id="d5a3e-1003">Descripción</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5a3e-1004">Función que devuelve una cadena que se proporciona como un token de autenticación de portador en solicitudes HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5a3e-1005">Establezca esta `true` opción para omitir el paso de negociación.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5a3e-1006">**Solo se admite cuando el transporte de WebSockets es el único transporte habilitado.**</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5a3e-1007">Esta configuración no se puede habilitar cuando se usa el servicio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5a3e-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5a3e-1009">Vacío</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1009">Empty</span></span> | <span data-ttu-id="d5a3e-1010">Un mapa de encabezados HTTP adicionales para enviar con cada solicitud HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5a3e-1011">En el cliente .NET, estas opciones pueden ser `WithUrl`modificadas por el delegado de opciones proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5a3e-1012">En el cliente JavaScript, estas opciones se pueden `withUrl`proporcionar en un objeto JavaScript proporcionado a:</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5a3e-1013">En el cliente Java, estas opciones se `HttpHubConnectionBuilder` pueden configurar con los métodos en el`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5a3e-1014">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d5a3e-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
