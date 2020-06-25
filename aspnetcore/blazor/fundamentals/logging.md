---
title: Registro de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo realizar registros en aplicaciones Blazor, incluido cómo configurar el nivel de registro y cómo escribir mensajes de registro desde componentes Razor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 841c4021d9217312b2601b0e775542c6455cca82
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240881"
---
# <a name="aspnet-core-blazor-logging"></a>Registro de Blazor en ASP.NET Core

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Configure el registro en aplicaciones WebAssembly de Blazor con la propiedad `WebAssemblyHostBuilder.Logging` establecida en `Program.Main`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

La propiedad `Logging` es del tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, de modo que todos los métodos de extensión disponibles en <xref:Microsoft.Extensions.Logging.ILoggingBuilder> también lo están en `Logging`.

La configuración de registro se puede cargar desde archivos de configuración de la aplicación. Para obtener más información, vea <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="blazor-server"></a>Servidor de Blazor

Para obtener instrucciones generales sobre el registro en ASP.NET Core, vea <xref:fundamentals/logging/index>.

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Registro de cliente .NET para SignalR en WebAssembly de Blazor

Inserte un proveedor <xref:Microsoft.Extensions.Logging.ILoggerProvider> para agregar un registrador `WebAssemblyConsoleLogger` a los proveedores de registro pasados a <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. A diferencia de los registradores <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> tradicionales, `WebAssemblyConsoleLogger` es un contenedor de API de registro específicas de explorador (por ejemplo, `console.log`). El uso de `WebAssemblyConsoleLogger` hace posible el registro en Mono dentro de un contexto de explorador.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a>Registro en componentes Razor

Los registradores respetan la configuración de inicio de la aplicación.

La directiva `using` para <xref:Microsoft.Extensions.Logging> es necesaria con el fin de permitir las finalizaciones de IntelliSense para las API, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.

En el siguiente ejemplo se muestra el registro con un <xref:Microsoft.Extensions.Logging.ILogger> en componentes Razor:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

En el siguiente ejemplo se muestra el registro con un <xref:Microsoft.Extensions.Logging.ILoggerFactory> en componentes Razor:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a>Recursos adicionales

* <xref:fundamentals/logging/index>
