---
title: Integración de la fábrica de cliente de gRPC en .NET Core
author: jamesnk
description: Aprenda a crear clientes gRPC mediante la fábrica de cliente.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 42b786b9a4d9b422ccf92d7a329979894a35b275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774722"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="93c3b-103">Integración de la fábrica de cliente de gRPC en .NET Core</span><span class="sxs-lookup"><span data-stu-id="93c3b-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="93c3b-104">La integración de gRPC con `HttpClientFactory` ofrece una manera centralizada de crear clientes gRPC.</span><span class="sxs-lookup"><span data-stu-id="93c3b-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="93c3b-105">Se puede usar como alternativa a la [configuración de instancias de cliente de gRPC independientes](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="93c3b-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="93c3b-106">La integración de fábrica está disponible en el paquete NuGet [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory).</span><span class="sxs-lookup"><span data-stu-id="93c3b-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="93c3b-107">La fábrica ofrece las ventajas siguientes:</span><span class="sxs-lookup"><span data-stu-id="93c3b-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="93c3b-108">Proporciona una ubicación central para configurar instancias de cliente de gRPC.</span><span class="sxs-lookup"><span data-stu-id="93c3b-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="93c3b-109">Administra la duración del objeto `HttpClientMessageHandler` subyacente.</span><span class="sxs-lookup"><span data-stu-id="93c3b-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="93c3b-110">Propagación automática de la fecha límite y la cancelación en un servicio gRPC de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93c3b-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="93c3b-111">Registro de clientes gRPC</span><span class="sxs-lookup"><span data-stu-id="93c3b-111">Register gRPC clients</span></span>

<span data-ttu-id="93c3b-112">Para registrar un cliente gRPC, se puede usar el método de extensión genérico `AddGrpcClient` dentro de `Startup.ConfigureServices`, y especificar la clase del cliente con tipo de gRPC y la dirección del servicio:</span><span class="sxs-lookup"><span data-stu-id="93c3b-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="93c3b-113">El tipo de cliente gRPC se registra como transitorio con la inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="93c3b-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="93c3b-114">Ahora el cliente se puede insertar y consumir directamente en los tipos creados por inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="93c3b-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="93c3b-115">Los controladores de ASP.NET Core MVC, los concentradores de SignalR y los servicios gRPC son lugares en los que se pueden insertar clientes gRPC de forma automática:</span><span class="sxs-lookup"><span data-stu-id="93c3b-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="93c3b-116">Configuración de HttpClient</span><span class="sxs-lookup"><span data-stu-id="93c3b-116">Configure HttpClient</span></span>

<span data-ttu-id="93c3b-117">`HttpClientFactory` crea el objeto `HttpClient` que usa el cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="93c3b-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="93c3b-118">Se pueden usar métodos `HttpClientFactory` estándar para agregar middleware de solicitud de salida o para configurar el objeto `HttpClientHandler` subyacente de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="93c3b-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="93c3b-119">Para obtener más información, vea [Realización de solicitudes HTTP con IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="93c3b-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="93c3b-120">Configuración del canal y los interceptores</span><span class="sxs-lookup"><span data-stu-id="93c3b-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="93c3b-121">Hay métodos específicos de gRPC disponibles para:</span><span class="sxs-lookup"><span data-stu-id="93c3b-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="93c3b-122">Configurar el canal subyacente de un cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="93c3b-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="93c3b-123">Agregar instancias de `Interceptor` que el cliente usará al realizar llamadas a gRPC.</span><span class="sxs-lookup"><span data-stu-id="93c3b-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="93c3b-124">Propagación de la fecha límite y la cancelación</span><span class="sxs-lookup"><span data-stu-id="93c3b-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="93c3b-125">Los clientes gRPC creados por la fábrica en un servicio gRPC se pueden configurar con `EnableCallContextPropagation()` para propagar automáticamente la fecha límite y el token de cancelación a las llamadas secundarias.</span><span class="sxs-lookup"><span data-stu-id="93c3b-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="93c3b-126">El método de extensión `EnableCallContextPropagation()` está disponible en el paquete NuGet [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory).</span><span class="sxs-lookup"><span data-stu-id="93c3b-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="93c3b-127">La propagación del contexto de llamada funciona mediante la lectura de la fecha límite y el token de cancelación del contexto de solicitud gRPC actual y su propagación automática a las llamadas salientes realizadas por el cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="93c3b-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="93c3b-128">La propagación del contexto de llamada es una excelente manera de garantizar que los escenarios complejos de gRPC anidados siempre propagan la fecha límite y la cancelación.</span><span class="sxs-lookup"><span data-stu-id="93c3b-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="93c3b-129">Para obtener más información sobre las fechas límite y la cancelación de RPC, vea [Ciclo de vida de RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="93c3b-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93c3b-130">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="93c3b-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
