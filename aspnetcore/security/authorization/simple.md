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
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272130"
---
# <a name="simple-authorization-in-aspnet-core"></a>Autorización simple en ASP.NET Core

<a name="security-authorization-simple"></a>

La autorización en ASP.NET Core se controla con <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> y sus diversos parámetros. En su forma más sencilla, aplicar el `[Authorize]` atributo a un controlador, una acción o una Razor página, limita el acceso a ese componente a cualquier usuario autenticado.

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

Ahora solo los usuarios autenticados pueden tener acceso a la `Logout` función.

También puede usar el `AllowAnonymous` atributo para permitir el acceso de usuarios no autenticados a acciones individuales. Por ejemplo:

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

Esto permitiría solo a los usuarios autenticados en `AccountController` , a excepción de la `Login` acción, que es accesible para todos, independientemente de su estado autenticado o no autenticado/anónimo.

> [!WARNING]
> `[AllowAnonymous]`omite todas las instrucciones de autorización. Si combina `[AllowAnonymous]` y cualquier `[Authorize]` atributo, `[Authorize]` se omiten los atributos. Por ejemplo, si se aplica `[AllowAnonymous]` en el nivel de controlador, `[Authorize]` se omite cualquier atributo del mismo controlador (o de cualquier acción que contenga).
