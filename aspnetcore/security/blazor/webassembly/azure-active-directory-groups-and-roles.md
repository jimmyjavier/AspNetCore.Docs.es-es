---
title: ASP.NET Core Blazor webassembly con Azure Active Directory grupos y roles
author: guardrex
description: Aprenda a configurar Blazor webassembly para usar Azure Active Directory grupos y roles.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: afdb5ddc4d4ed08d0f1ecaf7158af283dda6b302
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976886"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="ecaa9-103">Grupos de Azure AD, roles administrativos y roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="ecaa9-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="ecaa9-104">Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ecaa9-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="ecaa9-105">Azure Active Directory (AAD) proporciona varios métodos de autorización que se pueden combinar con ASP.NET Core identidad:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="ecaa9-106">Grupos definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="ecaa9-106">User-defined groups</span></span>
  * <span data-ttu-id="ecaa9-107">Seguridad</span><span class="sxs-lookup"><span data-stu-id="ecaa9-107">Security</span></span>
  * <span data-ttu-id="ecaa9-108">O365</span><span class="sxs-lookup"><span data-stu-id="ecaa9-108">O365</span></span>
  * <span data-ttu-id="ecaa9-109">Distribución</span><span class="sxs-lookup"><span data-stu-id="ecaa9-109">Distribution</span></span>
* <span data-ttu-id="ecaa9-110">Roles</span><span class="sxs-lookup"><span data-stu-id="ecaa9-110">Roles</span></span>
  * <span data-ttu-id="ecaa9-111">Roles administrativos integrados</span><span class="sxs-lookup"><span data-stu-id="ecaa9-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="ecaa9-112">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="ecaa9-112">User-defined roles</span></span>

<span data-ttu-id="ecaa9-113">Las instrucciones que se describen en este artículo se aplican a los escenarios de implementación de webassembly de increíblemente bajo descritos en los temas siguientes:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="ecaa9-114">Independiente con cuentas de Microsoft</span><span class="sxs-lookup"><span data-stu-id="ecaa9-114">Standalone with Microsoft Accounts</span></span>](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="ecaa9-115">Independiente con AAD</span><span class="sxs-lookup"><span data-stu-id="ecaa9-115">Standalone with AAD</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="ecaa9-116">Hospedado con AAD</span><span class="sxs-lookup"><span data-stu-id="ecaa9-116">Hosted with AAD</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="ecaa9-117">Grupos definidos por el usuario y roles administrativos integrados</span><span class="sxs-lookup"><span data-stu-id="ecaa9-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="ecaa9-118">Para configurar la aplicación en el Azure Portal para proporcionar una `groups` demanda de pertenencia, consulte los siguientes artículos de Azure.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="ecaa9-119">Asigne usuarios a grupos de AAD definidos por el usuario y roles administrativos integrados.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="ecaa9-120">Roles que usan grupos de seguridad de Azure AD</span><span class="sxs-lookup"><span data-stu-id="ecaa9-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="ecaa9-121">Atributo groupMembershipClaims</span><span class="sxs-lookup"><span data-stu-id="ecaa9-121">groupMembershipClaims attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="ecaa9-122">En los ejemplos siguientes se supone que un usuario está asignado al rol de *Administrador de facturación* integrado de AAD.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="ecaa9-123">La única `groups` demanda enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="ecaa9-124">La aplicación debe convertir la matriz JSON de grupos y roles en notificaciones individuales `group` con las que la aplicación puede crear [directivas](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="ecaa9-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="ecaa9-125">Extender `RemoteUserAccount` para incluir las propiedades de la matriz para grupos y roles.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-125">Extend `RemoteUserAccount` to include array properties for groups and roles.</span></span>

<span data-ttu-id="ecaa9-126">*CustomUserAccount.CS*:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-126">*CustomUserAccount.cs*:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="ecaa9-127">Cree un generador de usuario personalizado en la aplicación independiente o la aplicación cliente de una solución hospedada.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="ecaa9-128">El siguiente generador también está configurado para controlar `roles` matrices de notificaciones, que se tratan en la sección [roles definidos por el usuario](#user-defined-roles) :</span><span class="sxs-lookup"><span data-stu-id="ecaa9-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="ecaa9-129">No es necesario proporcionar código para quitar la declaración original `groups` , ya que el marco de trabajo la quita automáticamente.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="ecaa9-130">Registre el generador en `Program.Main` (*Program.CS*) de la aplicación independiente o la aplicación cliente de una solución hospedada:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-130">Register the factory in `Program.Main` (*Program.cs*) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="ecaa9-131">Cree una [Directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="ecaa9-132">En el ejemplo siguiente se crea una directiva para el rol de *Administrador de facturación* integrado de AAD:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="ecaa9-133">Para obtener una lista completa de los identificadores de objeto de rol de AAD, consulte la sección [identificadores de grupo de roles de AAD](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="ecaa9-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="ecaa9-134">En los ejemplos siguientes, la aplicación usa la directiva anterior para autorizar al usuario.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="ecaa9-135">El [componente AuthorizeView](xref:security/blazor/index#authorizeview-component) funciona con la Directiva:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-135">The [AuthorizeView component](xref:security/blazor/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="ecaa9-136">El acceso a un componente completo puede basarse en la Directiva mediante la Directiva de [ `[Authorize]` Directiva de atributo](xref:security/blazor/index#authorize-attribute) :</span><span class="sxs-lookup"><span data-stu-id="ecaa9-136">Access to an entire component can be based on the policy using the [`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) directive:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="ecaa9-137">Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="ecaa9-138">También se puede realizar una comprobación [de directiva en el código con lógica de procedimientos](xref:security/blazor/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="ecaa9-138">A policy check can also be [performed in code with procedural logic](xref:security/blazor/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a><span data-ttu-id="ecaa9-139">Roles definidos por el usuario</span><span class="sxs-lookup"><span data-stu-id="ecaa9-139">User-defined roles</span></span>

<span data-ttu-id="ecaa9-140">Una aplicación registrada en AAD también puede configurarse para usar roles definidos por el usuario.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="ecaa9-141">Para configurar la aplicación en el Azure Portal para proporcionar una `roles` solicitud de pertenencia, consulte [Cómo: agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="ecaa9-142">En el ejemplo siguiente se da por supuesto que una aplicación está configurada con dos roles:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="ecaa9-143">Aunque no puede asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, puede asignar usuarios a roles y recibir `roles` una demanda de usuarios con una cuenta de Azure estándar.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="ecaa9-144">Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="ecaa9-145">Se asignan varios roles en el Azure Portal al **_volver a agregar un usuario_** para cada asignación de roles adicional.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="ecaa9-146">La única `roles` demanda enviada por AAD presenta los roles definidos por el `appRoles`usuario como `value`s en una matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="ecaa9-147">La aplicación debe convertir la matriz JSON de roles en notificaciones individuales `role` .</span><span class="sxs-lookup"><span data-stu-id="ecaa9-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="ecaa9-148">El `CustomUserFactory` valor que se muestra en la sección [grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles) está configurado para actuar `roles` en una demanda con un valor de matriz JSON.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="ecaa9-149">Agregue y registre `CustomUserFactory` en la aplicación independiente o la aplicación cliente de una solución hospedada, tal como se muestra en la sección [grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="ecaa9-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="ecaa9-150">No es necesario proporcionar código para quitar la declaración original `roles` , ya que el marco de trabajo la quita automáticamente.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="ecaa9-151">En `Program.Main` la aplicación independiente o la aplicación cliente de una solución hospedada, especifique la demanda denominada`role`"" como la demanda de rol:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="ecaa9-152">Los enfoques de autorización de componentes son funcionales en este momento.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="ecaa9-153">Cualquiera de los mecanismos de autorización de los componentes puede `admin` usar el rol para autorizar al usuario:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="ecaa9-154">[Componente AuthorizeView](xref:security/blazor/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="ecaa9-154">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="ecaa9-155">Attribute Directive (ejemplo: `@attribute [Authorize(Roles = "admin")]`) [ `[Authorize]` ](xref:security/blazor/index#authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="ecaa9-155">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="ecaa9-156">[Lógica de procedimiento](xref:security/blazor/index#procedural-logic) (ejemplo `if (user.IsInRole("admin")) { ... }`:)</span><span class="sxs-lookup"><span data-stu-id="ecaa9-156">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="ecaa9-157">Se admiten varias pruebas de rol:</span><span class="sxs-lookup"><span data-stu-id="ecaa9-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="ecaa9-158">Identificadores de grupo de roles administrativos de AAD</span><span class="sxs-lookup"><span data-stu-id="ecaa9-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="ecaa9-159">Los identificadores de objeto presentados en la tabla siguiente se usan para crear `group` directivas para [las](xref:security/authorization/policies) notificaciones.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="ecaa9-160">Las directivas permiten a una aplicación autorizar a los usuarios para distintas actividades en una aplicación.</span><span class="sxs-lookup"><span data-stu-id="ecaa9-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="ecaa9-161">Para obtener más información, consulte la sección [grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="ecaa9-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="ecaa9-162">Rol administrativo de AAD</span><span class="sxs-lookup"><span data-stu-id="ecaa9-162">AAD Administrative Role</span></span> | <span data-ttu-id="ecaa9-163">Id. de objeto</span><span class="sxs-lookup"><span data-stu-id="ecaa9-163">Object ID</span></span>
--- | ---
<span data-ttu-id="ecaa9-164">Administrador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="ecaa9-164">Application administrator</span></span> | <span data-ttu-id="ecaa9-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="ecaa9-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="ecaa9-166">Desarrollador de aplicaciones</span><span class="sxs-lookup"><span data-stu-id="ecaa9-166">Application developer</span></span> | <span data-ttu-id="ecaa9-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="ecaa9-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="ecaa9-168">Administrador de autenticación</span><span class="sxs-lookup"><span data-stu-id="ecaa9-168">Authentication administrator</span></span> | <span data-ttu-id="ecaa9-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="ecaa9-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="ecaa9-170">Administrador de Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="ecaa9-170">Azure DevOps administrator</span></span> | <span data-ttu-id="ecaa9-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="ecaa9-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="ecaa9-172">Administrador de Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="ecaa9-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="ecaa9-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="ecaa9-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="ecaa9-174">Administrador de conjunto de claves de B2C IEF</span><span class="sxs-lookup"><span data-stu-id="ecaa9-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="ecaa9-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="ecaa9-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="ecaa9-176">Administrador de directivas de IEF B2C</span><span class="sxs-lookup"><span data-stu-id="ecaa9-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="ecaa9-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="ecaa9-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="ecaa9-178">Administrador de flujo de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="ecaa9-178">B2C user flow administrator</span></span> | <span data-ttu-id="ecaa9-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="ecaa9-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="ecaa9-180">Administrador de atributo de flujo de usuario B2C</span><span class="sxs-lookup"><span data-stu-id="ecaa9-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="ecaa9-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="ecaa9-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="ecaa9-182">Administrador de facturación</span><span class="sxs-lookup"><span data-stu-id="ecaa9-182">Billing administrator</span></span> | <span data-ttu-id="ecaa9-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="ecaa9-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="ecaa9-184">Administrador de aplicaciones en la nube</span><span class="sxs-lookup"><span data-stu-id="ecaa9-184">Cloud application administrator</span></span> | <span data-ttu-id="ecaa9-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="ecaa9-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="ecaa9-186">Administrador de dispositivos en la nube</span><span class="sxs-lookup"><span data-stu-id="ecaa9-186">Cloud device administrator</span></span> | <span data-ttu-id="ecaa9-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="ecaa9-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="ecaa9-188">Administrador de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="ecaa9-188">Compliance administrator</span></span> | <span data-ttu-id="ecaa9-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="ecaa9-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="ecaa9-190">Administrador de datos de cumplimiento</span><span class="sxs-lookup"><span data-stu-id="ecaa9-190">Compliance data administrator</span></span> | <span data-ttu-id="ecaa9-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="ecaa9-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="ecaa9-192">Administrador de acceso condicional</span><span class="sxs-lookup"><span data-stu-id="ecaa9-192">Conditional Access administrator</span></span> | <span data-ttu-id="ecaa9-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="ecaa9-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="ecaa9-194">Aprobador del acceso a la Caja de seguridad del cliente</span><span class="sxs-lookup"><span data-stu-id="ecaa9-194">Customer LockBox access approver</span></span> | <span data-ttu-id="ecaa9-195">c18d54a8-b13e-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="ecaa9-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="ecaa9-196">Administrador de análisis de escritorio</span><span class="sxs-lookup"><span data-stu-id="ecaa9-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="ecaa9-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="ecaa9-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="ecaa9-198">Lectores de directorios</span><span class="sxs-lookup"><span data-stu-id="ecaa9-198">Directory readers</span></span> | <span data-ttu-id="ecaa9-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="ecaa9-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="ecaa9-200">Administrador de Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="ecaa9-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="ecaa9-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="ecaa9-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="ecaa9-202">Administrador de Exchange</span><span class="sxs-lookup"><span data-stu-id="ecaa9-202">Exchange administrator</span></span> | <span data-ttu-id="ecaa9-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="ecaa9-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="ecaa9-204">Administrador Identity de proveedor externo</span><span class="sxs-lookup"><span data-stu-id="ecaa9-204">External Identity Provider administrator</span></span> | <span data-ttu-id="ecaa9-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="ecaa9-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="ecaa9-206">Administrador global</span><span class="sxs-lookup"><span data-stu-id="ecaa9-206">Global administrator</span></span> | <span data-ttu-id="ecaa9-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="ecaa9-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="ecaa9-208">Lector global</span><span class="sxs-lookup"><span data-stu-id="ecaa9-208">Global reader</span></span> | <span data-ttu-id="ecaa9-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="ecaa9-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="ecaa9-210">Administrador de grupos</span><span class="sxs-lookup"><span data-stu-id="ecaa9-210">Groups administrator</span></span> | <span data-ttu-id="ecaa9-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="ecaa9-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="ecaa9-212">Invitador de usuarios</span><span class="sxs-lookup"><span data-stu-id="ecaa9-212">Guest inviter</span></span> | <span data-ttu-id="ecaa9-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="ecaa9-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="ecaa9-214">Administrador del departamento de soporte técnico</span><span class="sxs-lookup"><span data-stu-id="ecaa9-214">Helpdesk administrator</span></span> | <span data-ttu-id="ecaa9-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="ecaa9-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="ecaa9-216">Administrador de Intune</span><span class="sxs-lookup"><span data-stu-id="ecaa9-216">Intune administrator</span></span> | <span data-ttu-id="ecaa9-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="ecaa9-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="ecaa9-218">Administrador de Kaizala</span><span class="sxs-lookup"><span data-stu-id="ecaa9-218">Kaizala administrator</span></span> | <span data-ttu-id="ecaa9-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="ecaa9-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="ecaa9-220">Administrador de licencias</span><span class="sxs-lookup"><span data-stu-id="ecaa9-220">License administrator</span></span> | <span data-ttu-id="ecaa9-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="ecaa9-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="ecaa9-222">Lector de privacidad del Centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="ecaa9-222">Message center privacy reader</span></span> | <span data-ttu-id="ecaa9-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="ecaa9-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="ecaa9-224">Lector del centro de mensajes</span><span class="sxs-lookup"><span data-stu-id="ecaa9-224">Message center reader</span></span> | <span data-ttu-id="ecaa9-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="ecaa9-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="ecaa9-226">Administrador de aplicaciones de Office</span><span class="sxs-lookup"><span data-stu-id="ecaa9-226">Office apps administrator</span></span> | <span data-ttu-id="ecaa9-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="ecaa9-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="ecaa9-228">Administrador de contraseñas</span><span class="sxs-lookup"><span data-stu-id="ecaa9-228">Password administrator</span></span> | <span data-ttu-id="ecaa9-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="ecaa9-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="ecaa9-230">Administrador de Power BI</span><span class="sxs-lookup"><span data-stu-id="ecaa9-230">Power BI administrator</span></span> | <span data-ttu-id="ecaa9-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="ecaa9-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="ecaa9-232">Administrador de Power Platform</span><span class="sxs-lookup"><span data-stu-id="ecaa9-232">Power platform administrator</span></span> | <span data-ttu-id="ecaa9-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="ecaa9-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="ecaa9-234">Administrador de autenticación con privilegios</span><span class="sxs-lookup"><span data-stu-id="ecaa9-234">Privileged authentication administrator</span></span> | <span data-ttu-id="ecaa9-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="ecaa9-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="ecaa9-236">Administrador de roles con privilegios</span><span class="sxs-lookup"><span data-stu-id="ecaa9-236">Privileged role administrator</span></span> | <span data-ttu-id="ecaa9-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="ecaa9-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="ecaa9-238">Lector de informes</span><span class="sxs-lookup"><span data-stu-id="ecaa9-238">Reports reader</span></span> | <span data-ttu-id="ecaa9-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="ecaa9-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="ecaa9-240">Administrador de búsqueda</span><span class="sxs-lookup"><span data-stu-id="ecaa9-240">Search administrator</span></span> | <span data-ttu-id="ecaa9-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="ecaa9-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="ecaa9-242">Editor de búsqueda</span><span class="sxs-lookup"><span data-stu-id="ecaa9-242">Search editor</span></span> | <span data-ttu-id="ecaa9-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="ecaa9-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="ecaa9-244">Administrador de seguridad</span><span class="sxs-lookup"><span data-stu-id="ecaa9-244">Security administrator</span></span> | <span data-ttu-id="ecaa9-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="ecaa9-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="ecaa9-246">Operador de seguridad</span><span class="sxs-lookup"><span data-stu-id="ecaa9-246">Security operator</span></span> | <span data-ttu-id="ecaa9-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="ecaa9-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="ecaa9-248">Lector de seguridad</span><span class="sxs-lookup"><span data-stu-id="ecaa9-248">Security reader</span></span> | <span data-ttu-id="ecaa9-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="ecaa9-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="ecaa9-250">Administrador del soporte técnico del servicio</span><span class="sxs-lookup"><span data-stu-id="ecaa9-250">Service support administrator</span></span> | <span data-ttu-id="ecaa9-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="ecaa9-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="ecaa9-252">Administrador de SharePoint</span><span class="sxs-lookup"><span data-stu-id="ecaa9-252">SharePoint administrator</span></span> | <span data-ttu-id="ecaa9-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="ecaa9-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="ecaa9-254">Administrador de Skype Empresarial</span><span class="sxs-lookup"><span data-stu-id="ecaa9-254">Skype for Business administrator</span></span> | <span data-ttu-id="ecaa9-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="ecaa9-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="ecaa9-256">Administrador de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="ecaa9-256">Teams Communications Administrator</span></span> | <span data-ttu-id="ecaa9-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="ecaa9-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="ecaa9-258">Ingeniero de soporte técnico de comunicaciones de Teams</span><span class="sxs-lookup"><span data-stu-id="ecaa9-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="ecaa9-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="ecaa9-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="ecaa9-260">Especialista en comunicaciones de equipos</span><span class="sxs-lookup"><span data-stu-id="ecaa9-260">Teams Communications Specialist</span></span> | <span data-ttu-id="ecaa9-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="ecaa9-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="ecaa9-262">Administrador de servicios de Teams</span><span class="sxs-lookup"><span data-stu-id="ecaa9-262">Teams Service Administrator</span></span> | <span data-ttu-id="ecaa9-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="ecaa9-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="ecaa9-264">Administrador de usuarios</span><span class="sxs-lookup"><span data-stu-id="ecaa9-264">User administrator</span></span> | <span data-ttu-id="ecaa9-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="ecaa9-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ecaa9-266">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="ecaa9-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
