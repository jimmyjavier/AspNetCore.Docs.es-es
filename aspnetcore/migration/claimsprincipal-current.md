---
title: Migrar desde ClaimsPrincipal. Current
author: mjrousos
description: Obtenga información sobre cómo migrar de ClaimsPrincipal. Current para recuperar la identidad del usuario autenticado actual y las notificaciones en ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776095"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrar desde ClaimsPrincipal. Current

En los proyectos de ASP.NET 4. x, era habitual usar [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) para recuperar la identidad y las notificaciones del usuario autenticado actual. En ASP.NET Core, esta propiedad ya no se establece. El código que depende de él debe actualizarse para obtener la identidad del usuario autenticado actual a través de un medio diferente.

## <a name="context-specific-data-instead-of-static-data"></a>Datos específicos del contexto en lugar de datos estáticos

Cuando se usa ASP.NET Core, los valores de `ClaimsPrincipal.Current` y `Thread.CurrentPrincipal` no están establecidos. Estas propiedades representan el estado estático, que normalmente evita ASP.NET Core. En su lugar, la arquitectura de ASP.NET Core es recuperar las dependencias (por ejemplo, la identidad del usuario actual) de las colecciones de servicios específicas del contexto (mediante el modelo de [inserción de dependencias](xref:fundamentals/dependency-injection) ). Lo que es más `Thread.CurrentPrincipal` , es un subproceso estático, por lo que puede que no conserve los cambios `ClaimsPrincipal.Current` en algunos `Thread.CurrentPrincipal` escenarios asincrónicos (y simplemente llama a las llamadas de forma predeterminada).

Para comprender los tipos de problemas que los miembros estáticos de subproceso pueden provocar en escenarios asincrónicos, considere el siguiente fragmento de código:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

El código de ejemplo anterior `Thread.CurrentPrincipal` establece y comprueba su valor antes y después de esperar una llamada asincrónica. `Thread.CurrentPrincipal`es específico del *subproceso* en el que se establece y es probable que el método reanude la ejecución en un subproceso diferente después de la espera. Por consiguiente, `Thread.CurrentPrincipal` está presente cuando se comprueba por primera vez pero es NULL después de la `await Task.Yield()`llamada a.

Obtener la identidad del usuario actual de la colección de servicios de DI de la aplicación también es más fácil de probar, ya que las identidades de prueba se pueden insertar fácilmente.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Recuperar el usuario actual en una aplicación ASP.NET Core

Hay varias opciones para recuperar el usuario autenticado actual `ClaimsPrincipal` en ASP.net Core en lugar de: `ClaimsPrincipal.Current`

* **ControllerBase. usuario**. Los controladores MVC pueden tener acceso al usuario autenticado actual con su propiedad [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Los componentes con acceso al actual `HttpContext` (por ejemplo, middleware) pueden obtener el del `ClaimsPrincipal` usuario actual de [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Pasado del llamador**. A menudo se llama a las `HttpContext` bibliotecas que no tienen acceso al actual desde los controladores o los componentes de middleware, y puede tener la identidad del usuario actual pasada como argumento.
* **IHttpContextAccessor**. El proyecto que se va a migrar a ASP.NET Core puede ser demasiado grande para pasar fácilmente la identidad del usuario actual a todas las ubicaciones necesarias. En tales casos, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) se puede usar como solución alternativa. `IHttpContextAccessor`puede tener acceso al actual `HttpContext` (si existe). Si se usa DI, vea <xref:fundamentals/httpcontext>. Una solución a corto plazo para obtener la identidad del usuario actual en el código que todavía no se ha actualizado para funcionar con la arquitectura basada en DI de ASP.NET Core sería:

  * Haga `IHttpContextAccessor` que esté disponible en el contenedor de di llamando `Startup.ConfigureServices`a [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) en.
  * Obtiene una instancia de `IHttpContextAccessor` durante el inicio y la almacena en una variable estática. La instancia de se pone a disposición del código que antes recuperaba el usuario actual de una propiedad estática.
  * Recupere el usuario actual `ClaimsPrincipal` mediante `HttpContextAccessor.HttpContext?.User`. Si este código se usa fuera del contexto de una solicitud HTTP, el `HttpContext` valor de es NULL.

La última opción, el uso `IHttpContextAccessor` de una instancia almacenada en una variable estática, es contraria a los principios ASP.net Core (la preferencia de dependencias insertadas a las dependencias estáticas). En su lugar, `IHttpContextAccessor` Planee la recuperación de instancias de la inserción de dependencias. Sin embargo, una aplicación auxiliar estática puede ser un puente útil a la hora de migrar aplicaciones de ASP.NET grandes existentes `ClaimsPrincipal.Current`que usaban previamente.
