---
title: Inserción de dependencias en controladores de requisitos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo insertar controladores de requisitos de autorización en una aplicación ASP.NET Core mediante la inserción de dependencias.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 16285f6f731455d6e45a04f82437793891a77668
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775125"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Inserción de dependencias en controladores de requisitos en ASP.NET Core

<a name="security-authorization-di"></a>

Los [controladores de autorización deben registrarse](xref:security/authorization/policies#handler-registration) en la colección de servicios durante la configuración (mediante la [inserción de dependencias](xref:fundamentals/dependency-injection)).

Supongamos que tiene un repositorio de reglas que desea evaluar dentro de un controlador de autorización y que ese repositorio se registró en la colección de servicios. La autorización se resolverá y se insertará en el constructor.

Por ejemplo, si desea utilizar ASP. La infraestructura de registro de la red que se `ILoggerFactory` desea insertar en el controlador. Este tipo de controlador podría ser similar al siguiente:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Registraría el controlador con `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Cuando se inicia la aplicación, se creará una instancia del controlador y DI insertará el registrado `ILoggerFactory` en el constructor.

> [!NOTE]
> Los controladores que usan Entity Framework no deben registrarse como singletons.
