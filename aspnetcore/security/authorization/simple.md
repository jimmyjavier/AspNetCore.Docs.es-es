---
<span data-ttu-id="cc796-101">Título: autorización sencilla en ASP.NET Core autor: Rick-Anderson Description: Aprenda a usar el atributo Authorize para restringir el acceso a los controladores y acciones de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc796-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="cc796-102">MS. Author: Riande ms. Date: 10/14/2016 no-LOC:</span><span class="sxs-lookup"><span data-stu-id="cc796-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="cc796-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cc796-103">'Blazor'</span></span>
- <span data-ttu-id="cc796-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cc796-104">'Identity'</span></span>
- <span data-ttu-id="cc796-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cc796-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cc796-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cc796-106">'Razor'</span></span>
- <span data-ttu-id="cc796-107">' SignalR ' UID: seguridad/autorización/simple</span><span class="sxs-lookup"><span data-stu-id="cc796-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="cc796-108">Autorización simple en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc796-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="cc796-109">La autorización en ASP.NET Core se controla con <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> y sus diversos parámetros.</span><span class="sxs-lookup"><span data-stu-id="cc796-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="cc796-110">En su forma más sencilla, aplicar el `[Authorize]` atributo a un controlador, una acción o una Razor página, limita el acceso a ese componente a cualquier usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="cc796-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="cc796-111">Por ejemplo, el código siguiente limita el acceso a `AccountController` a cualquier usuario autenticado.</span><span class="sxs-lookup"><span data-stu-id="cc796-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="cc796-112">Si desea aplicar la autorización a una acción en lugar del controlador, aplique el `AuthorizeAttribute` atributo a la propia acción:</span><span class="sxs-lookup"><span data-stu-id="cc796-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="cc796-113">Ahora solo los usuarios autenticados pueden tener acceso a la `Logout` función.</span><span class="sxs-lookup"><span data-stu-id="cc796-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="cc796-114">También puede usar el `AllowAnonymous` atributo para permitir el acceso de usuarios no autenticados a acciones individuales.</span><span class="sxs-lookup"><span data-stu-id="cc796-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="cc796-115">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cc796-115">For example:</span></span>

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

<span data-ttu-id="cc796-116">Esto permitiría solo a los usuarios autenticados en `AccountController` , a excepción de la `Login` acción, que es accesible para todos, independientemente de su estado autenticado o no autenticado/anónimo.</span><span class="sxs-lookup"><span data-stu-id="cc796-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="cc796-117">`[AllowAnonymous]`omite todas las instrucciones de autorización.</span><span class="sxs-lookup"><span data-stu-id="cc796-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="cc796-118">Si combina `[AllowAnonymous]` y cualquier `[Authorize]` atributo, `[Authorize]` se omiten los atributos.</span><span class="sxs-lookup"><span data-stu-id="cc796-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="cc796-119">Por ejemplo, si se aplica `[AllowAnonymous]` en el nivel de controlador, `[Authorize]` se omite cualquier atributo del mismo controlador (o de cualquier acción que contenga).</span><span class="sxs-lookup"><span data-stu-id="cc796-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
