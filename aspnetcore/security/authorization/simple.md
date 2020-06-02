---
Título: autorización sencilla en ASP.NET Core autor: Rick-Anderson Description: Aprenda a usar el atributo Authorize para restringir el acceso a los controladores y acciones de ASP.NET Core.
MS. Author: Riande ms. Date: 10/14/2016 no-LOC:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' UID: seguridad/autorización/simple

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
