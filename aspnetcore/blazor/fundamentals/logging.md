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
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="dd5cd-103">Registro de Blazor en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd5cd-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="dd5cd-104"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="dd5cd-104"> WebAssembly</span></span>

<span data-ttu-id="dd5cd-105">Configure el registro en aplicaciones WebAssembly de Blazor con la propiedad `WebAssemblyHostBuilder.Logging` establecida en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="dd5cd-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="dd5cd-106">La propiedad `Logging` es del tipo <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, de modo que todos los métodos de extensión disponibles en <xref:Microsoft.Extensions.Logging.ILoggingBuilder> también lo están en `Logging`.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="dd5cd-107">La configuración de registro se puede cargar desde archivos de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="dd5cd-108">Para obtener más información, vea <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-108">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="dd5cd-109">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="dd5cd-109">Blazor Server</span></span>

<span data-ttu-id="dd5cd-110">Para obtener instrucciones generales sobre el registro en ASP.NET Core, vea <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a><span data-ttu-id="dd5cd-111">Registro de cliente .NET para SignalR en WebAssembly de Blazor</span><span class="sxs-lookup"><span data-stu-id="dd5cd-111">Blazor WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="dd5cd-112">Inserte un proveedor <xref:Microsoft.Extensions.Logging.ILoggerProvider> para agregar un registrador `WebAssemblyConsoleLogger` a los proveedores de registro pasados a <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="dd5cd-113">A diferencia de los registradores <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> tradicionales, `WebAssemblyConsoleLogger` es un contenedor de API de registro específicas de explorador (por ejemplo, `console.log`).</span><span class="sxs-lookup"><span data-stu-id="dd5cd-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="dd5cd-114">El uso de `WebAssemblyConsoleLogger` hace posible el registro en Mono dentro de un contexto de explorador.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="dd5cd-115">Registro en componentes Razor</span><span class="sxs-lookup"><span data-stu-id="dd5cd-115">Log in Razor components</span></span>

<span data-ttu-id="dd5cd-116">Los registradores respetan la configuración de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="dd5cd-117">La directiva `using` para <xref:Microsoft.Extensions.Logging> es necesaria con el fin de permitir las finalizaciones de IntelliSense para las API, como <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> y <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="dd5cd-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="dd5cd-118">En el siguiente ejemplo se muestra el registro con un <xref:Microsoft.Extensions.Logging.ILogger> en componentes Razor:</span><span class="sxs-lookup"><span data-stu-id="dd5cd-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="dd5cd-119">En el siguiente ejemplo se muestra el registro con un <xref:Microsoft.Extensions.Logging.ILoggerFactory> en componentes Razor:</span><span class="sxs-lookup"><span data-stu-id="dd5cd-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="dd5cd-120">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="dd5cd-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
