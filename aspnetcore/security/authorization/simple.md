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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="484b0-103">Autorización simple en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="484b0-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="484b0-104">La autorización en MVC se controla mediante `AuthorizeAttribute` el atributo y sus diversos parámetros.</span><span class="sxs-lookup"><span data-stu-id="484b0-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="484b0-105">En su forma más sencilla, aplicar `AuthorizeAttribute` el atributo a un controlador o una acción limita el acceso al controlador o la acción a cualquier usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="484b0-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="484b0-106">Por ejemplo, el código siguiente limita el acceso a `AccountController` a cualquier usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="484b0-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="484b0-107">Si desea aplicar la autorización a una acción en lugar del controlador, aplique el `AuthorizeAttribute` atributo a la propia acción:</span><span class="sxs-lookup"><span data-stu-id="484b0-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="484b0-108">Ahora solo los usuarios autenticados pueden tener `Logout` acceso a la función.</span><span class="sxs-lookup"><span data-stu-id="484b0-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="484b0-109">También puede usar el atributo `AllowAnonymous` para permitir el acceso de usuarios no autenticados a acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="484b0-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="484b0-110">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="484b0-110">For example:</span></span>

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

<span data-ttu-id="484b0-111">Esto permitiría solo a los usuarios autenticados `AccountController`en, a excepción `Login` de la acción, que es accesible para todos, independientemente de su estado autenticado o no autenticado/anónimo.</span><span class="sxs-lookup"><span data-stu-id="484b0-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="484b0-112">`[AllowAnonymous]`omite todas las instrucciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="484b0-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="484b0-113">Si combina `[AllowAnonymous]` y cualquier `[Authorize]` atributo, se omiten los `[Authorize]` atributos.</span><span class="sxs-lookup"><span data-stu-id="484b0-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="484b0-114">Por ejemplo, si se `[AllowAnonymous]` aplica en el nivel de controlador `[Authorize]` , se omite cualquier atributo del mismo controlador (o de cualquier acción que contenga).</span><span class="sxs-lookup"><span data-stu-id="484b0-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
