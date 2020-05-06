---
title: ASP.NET Core SignalR host en los servicios en segundo plano
author: bradygaster
description: Obtenga información sobre cómo enviar mensajes SignalR a los clientes desde clases BackgroundService de .net Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777298"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="abc77-103">ASP.NET Core SignalR host en los servicios en segundo plano</span><span class="sxs-lookup"><span data-stu-id="abc77-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="abc77-104">Por el [Brady](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="abc77-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="abc77-105">En este artículo se proporcionan instrucciones para:</span><span class="sxs-lookup"><span data-stu-id="abc77-105">This article provides guidance for:</span></span>

* <span data-ttu-id="abc77-106">Hospedar SignalR los concentradores mediante un proceso de trabajo en segundo plano hospedado con ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="abc77-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="abc77-107">Envío de mensajes a clientes conectados desde un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)de .net Core.</span><span class="sxs-lookup"><span data-stu-id="abc77-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="abc77-108">[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="abc77-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="abc77-109">Habilitar SignalR en el inicio</span><span class="sxs-lookup"><span data-stu-id="abc77-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="abc77-110">Hospedar SignalR ASP.net Core hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación Web ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="abc77-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="abc77-111">En el `Startup.ConfigureServices` método, la `services.AddSignalR` llamada a agrega los servicios necesarios a la capa de ASP.net Core de la inserción de SignalRdependencias (di) para admitir.</span><span class="sxs-lookup"><span data-stu-id="abc77-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="abc77-112">En `Startup.Configure`, se `MapHub` llama al método en la `UseEndpoints` devolución de llamada para conectar los puntos de conexión del concentrador en el ASP.net Core canalización de solicitudes.</span><span class="sxs-lookup"><span data-stu-id="abc77-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="abc77-113">Hospedar SignalR ASP.net Core hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación Web ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="abc77-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="abc77-114">En el `Startup.ConfigureServices` método, la `services.AddSignalR` llamada a agrega los servicios necesarios a la capa de ASP.net Core de la inserción de SignalRdependencias (di) para admitir.</span><span class="sxs-lookup"><span data-stu-id="abc77-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="abc77-115">En `Startup.Configure`, se `UseSignalR` llama al método para conectar los puntos de conexión del concentrador en la canalización de solicitud de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="abc77-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="abc77-116">En el ejemplo anterior, la `ClockHub` clase implementa la `Hub<T>` clase para crear un concentrador fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="abc77-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="abc77-117">`ClockHub` Se ha configurado en la `Startup` clase para responder a las solicitudes en el extremo `/hubs/clock`.</span><span class="sxs-lookup"><span data-stu-id="abc77-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="abc77-118">Para obtener más información sobre los concentradores fuertemente tipados, consulte [uso de hubs SignalR en para ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="abc77-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="abc77-119">Esta funcionalidad no se limita a la clase de [>Hub\<t](xref:Microsoft.AspNetCore.SignalR.Hub`1) .</span><span class="sxs-lookup"><span data-stu-id="abc77-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="abc77-120">Cualquier clase que herede de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), también funcionará.</span><span class="sxs-lookup"><span data-stu-id="abc77-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="abc77-121">La interfaz utilizada por el fuertemente tipado `ClockHub` es la `IClock` interfaz.</span><span class="sxs-lookup"><span data-stu-id="abc77-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="abc77-122">Llamar a SignalR un centro desde un servicio en segundo plano</span><span class="sxs-lookup"><span data-stu-id="abc77-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="abc77-123">Durante el inicio, `Worker` la clase, `BackgroundService`a, se habilita `AddHostedService`mediante.</span><span class="sxs-lookup"><span data-stu-id="abc77-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="abc77-124">Puesto SignalR que también se habilita durante la `Startup` fase, en la que cada concentrador se adjunta a un punto de conexión individual en la CANALización de solicitudes HTTP de ASP.net Core `IHubContext<T>` , cada concentrador se representa mediante un en el servidor.</span><span class="sxs-lookup"><span data-stu-id="abc77-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="abc77-125">Mediante el uso de las características DI de ASP.NET Core, otras clases a las que se `BackgroundService` ha creado una instancia de la capa Razor de hospedaje, como las clases, las clases de controlador de MVC o los `IHubContext<ClockHub, IClock>` modelos de página, pueden obtener referencias a los concentradores del lado servidor aceptando instancias de durante la construcción.</span><span class="sxs-lookup"><span data-stu-id="abc77-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="abc77-126">A medida `ExecuteAsync` que se llama al método de forma iterativa en el servicio en segundo plano, la fecha y hora actuales del servidor se envían a `ClockHub`los clientes conectados mediante.</span><span class="sxs-lookup"><span data-stu-id="abc77-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="abc77-127">Reaccionar a SignalR eventos con servicios en segundo plano</span><span class="sxs-lookup"><span data-stu-id="abc77-127">React to SignalR events with background services</span></span>

<span data-ttu-id="abc77-128">Al igual que una aplicación de una sola página que SignalR usa el cliente de JavaScript para o una aplicación de escritorio <xref:signalr/dotnet-client>de .net `BackgroundService` puede `IHostedService` usar mediante el, también se puede usar SignalR una implementación de o para conectarse a los concentradores y responder a eventos.</span><span class="sxs-lookup"><span data-stu-id="abc77-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="abc77-129">La `ClockHubClient` clase implementa la `IClock` interfaz y la `IHostedService` interfaz.</span><span class="sxs-lookup"><span data-stu-id="abc77-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="abc77-130">De este modo, se puede habilitar `Startup` durante la ejecución continua y responder a los eventos del concentrador desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="abc77-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="abc77-131">Durante la `ClockHubClient` inicialización, crea una instancia de `HubConnection` y habilita el `IClock.ShowTime` método como controlador para el evento del `ShowTime` centro.</span><span class="sxs-lookup"><span data-stu-id="abc77-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="abc77-132">En la `IHostedService.StartAsync` implementación de, `HubConnection` se inicia de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="abc77-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="abc77-133">Durante el `IHostedService.StopAsync` método, `HubConnection` se elimina de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="abc77-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="abc77-134">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="abc77-134">Additional resources</span></span>

* [<span data-ttu-id="abc77-135">Introducción</span><span class="sxs-lookup"><span data-stu-id="abc77-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="abc77-136">Concentradores</span><span class="sxs-lookup"><span data-stu-id="abc77-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="abc77-137">Publicación en Azure</span><span class="sxs-lookup"><span data-stu-id="abc77-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="abc77-138">Concentradores fuertemente tipados</span><span class="sxs-lookup"><span data-stu-id="abc77-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
