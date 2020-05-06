---
title: Migre desde Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0
author: pakrym
description: Obtenga información sobre cómo migrar una aplicación de non-ASP.NET Core que usa Microsoft. Extensions. Logging de 2,1 a 2,2 o 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777064"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="4b349-103">Migre desde Microsoft. Extensions. Logging 2,1 a 2,2 o 3,0</span><span class="sxs-lookup"><span data-stu-id="4b349-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="4b349-104">En este artículo se describen los pasos comunes para migrar una aplicación de non-ASP.NET Core que `Microsoft.Extensions.Logging` usa de 2,1 a 2,2 o 3,0.</span><span class="sxs-lookup"><span data-stu-id="4b349-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="4b349-105">2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="4b349-105">2.1 to 2.2</span></span>

<span data-ttu-id="4b349-106">Crear `ServiceCollection` y llamar manualmente `AddLogging`a.</span><span class="sxs-lookup"><span data-stu-id="4b349-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="4b349-107">ejemplo 2,1:</span><span class="sxs-lookup"><span data-stu-id="4b349-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="4b349-108">ejemplo 2,2:</span><span class="sxs-lookup"><span data-stu-id="4b349-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="4b349-109">de 2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="4b349-109">2.1 to 3.0</span></span>

<span data-ttu-id="4b349-110">En 3,0, use `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="4b349-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="4b349-111">ejemplo 2,1:</span><span class="sxs-lookup"><span data-stu-id="4b349-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="4b349-112">ejemplo 3,0:</span><span class="sxs-lookup"><span data-stu-id="4b349-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="4b349-113">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4b349-113">Additional resources</span></span>

<xref:fundamentals/logging/index>