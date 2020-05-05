---
title: Autorización simple en ASP.NET Core
author: rick-anderson
description: Aprenda a usar el atributo Authorize para restringir el acceso a ASP.NET Core controladores y acciones.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775640"
---
# <a name="simple-authorization-in-aspnet-core"></a>Autorización simple en ASP.NET Core

<a name="security-authorization-simple"></a>

La autorización en MVC se controla mediante `AuthorizeAttribute` el atributo y sus diversos parámetros. En su forma más sencilla, aplicar `AuthorizeAttribute` el atributo a un controlador o una acción limita el acceso al controlador o la acción a cualquier usuario autenticado.

Por ejemplo, el código siguiente limita el acceso a `AccountController` a cualquier usuario autenticado.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Si desea aplicar la autorización a una acción en lugar del controlador, aplique el `AuthorizeAttribute` atributo a la propia acción:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Ahora solo los usuarios autenticados pueden tener `Logout` acceso a la función.

También puede usar el atributo `AllowAnonymous` para permitir el acceso de usuarios no autenticados a acciones individuales. Por ejemplo:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Esto permitiría solo a los usuarios autenticados `AccountController`en, a excepción `Login` de la acción, que es accesible para todos, independientemente de su estado autenticado o no autenticado/anónimo.

> [!WARNING]
> `[AllowAnonymous]`omite todas las instrucciones de autorización. Si combina `[AllowAnonymous]` y cualquier `[Authorize]` atributo, se omiten los `[Authorize]` atributos. Por ejemplo, si se `[AllowAnonymous]` aplica en el nivel de controlador `[Authorize]` , se omite cualquier atributo del mismo controlador (o de cualquier acción que contenga).
