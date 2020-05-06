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
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core SignalR host en los servicios en segundo plano

Por el [Brady](https://twitter.com/bradygaster)

En este artículo se proporcionan instrucciones para:

* Hospedar SignalR los concentradores mediante un proceso de trabajo en segundo plano hospedado con ASP.net Core.
* Envío de mensajes a clientes conectados desde un [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)de .net Core.

[Ver o descargar el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(cómo descargarlo)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Habilitar SignalR en el inicio

::: moniker range=">= aspnetcore-3.0"

Hospedar SignalR ASP.net Core hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación Web ASP.net Core. En el `Startup.ConfigureServices` método, la `services.AddSignalR` llamada a agrega los servicios necesarios a la capa de ASP.net Core de la inserción de SignalRdependencias (di) para admitir. En `Startup.Configure`, se `MapHub` llama al método en la `UseEndpoints` devolución de llamada para conectar los puntos de conexión del concentrador en el ASP.net Core canalización de solicitudes.

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

Hospedar SignalR ASP.net Core hubs en el contexto de un proceso de trabajo en segundo plano es idéntico a hospedar un centro en una aplicación Web ASP.net Core. En el `Startup.ConfigureServices` método, la `services.AddSignalR` llamada a agrega los servicios necesarios a la capa de ASP.net Core de la inserción de SignalRdependencias (di) para admitir. En `Startup.Configure`, se `UseSignalR` llama al método para conectar los puntos de conexión del concentrador en la canalización de solicitud de ASP.net Core.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

En el ejemplo anterior, la `ClockHub` clase implementa la `Hub<T>` clase para crear un concentrador fuertemente tipado. `ClockHub` Se ha configurado en la `Startup` clase para responder a las solicitudes en el extremo `/hubs/clock`.

Para obtener más información sobre los concentradores fuertemente tipados, consulte [uso de hubs SignalR en para ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Esta funcionalidad no se limita a la clase de [>Hub\<t](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Cualquier clase que herede de [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), como [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), también funcionará.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

La interfaz utilizada por el fuertemente tipado `ClockHub` es la `IClock` interfaz.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Llamar a SignalR un centro desde un servicio en segundo plano

Durante el inicio, `Worker` la clase, `BackgroundService`a, se habilita `AddHostedService`mediante.

```csharp
services.AddHostedService<Worker>();
```

Puesto SignalR que también se habilita durante la `Startup` fase, en la que cada concentrador se adjunta a un punto de conexión individual en la CANALización de solicitudes HTTP de ASP.net Core `IHubContext<T>` , cada concentrador se representa mediante un en el servidor. Mediante el uso de las características DI de ASP.NET Core, otras clases a las que se `BackgroundService` ha creado una instancia de la capa Razor de hospedaje, como las clases, las clases de controlador de MVC o los `IHubContext<ClockHub, IClock>` modelos de página, pueden obtener referencias a los concentradores del lado servidor aceptando instancias de durante la construcción.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

A medida `ExecuteAsync` que se llama al método de forma iterativa en el servicio en segundo plano, la fecha y hora actuales del servidor se envían a `ClockHub`los clientes conectados mediante.

## <a name="react-to-signalr-events-with-background-services"></a>Reaccionar a SignalR eventos con servicios en segundo plano

Al igual que una aplicación de una sola página que SignalR usa el cliente de JavaScript para o una aplicación de escritorio <xref:signalr/dotnet-client>de .net `BackgroundService` puede `IHostedService` usar mediante el, también se puede usar SignalR una implementación de o para conectarse a los concentradores y responder a eventos.

La `ClockHubClient` clase implementa la `IClock` interfaz y la `IHostedService` interfaz. De este modo, se puede habilitar `Startup` durante la ejecución continua y responder a los eventos del concentrador desde el servidor.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Durante la `ClockHubClient` inicialización, crea una instancia de `HubConnection` y habilita el `IClock.ShowTime` método como controlador para el evento del `ShowTime` centro.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

En la `IHostedService.StartAsync` implementación de, `HubConnection` se inicia de forma asincrónica.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Durante el `IHostedService.StopAsync` método, `HubConnection` se elimina de forma asincrónica.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Recursos adicionales

* [Introducción](xref:tutorials/signalr)
* [Concentradores](xref:signalr/hubs)
* [Publicación en Azure](xref:signalr/publish-to-azure-web-app)
* [Concentradores fuertemente tipados](xref:signalr/hubs#strongly-typed-hubs)
