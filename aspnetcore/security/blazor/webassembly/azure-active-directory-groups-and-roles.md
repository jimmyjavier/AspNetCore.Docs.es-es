---
title: ASP.NET Core Blazor Webassembly con Azure Active Directory grupos y roles
author: guardrex
description: Aprenda a configurar Blazor Webassembly para usar Azure Active Directory grupos y roles.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 3ed06cca7e20da381b870e642a6c616b2578cd0a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451880"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Grupos de Azure AD, roles administrativos y roles definidos por el usuario

Por [Luke Latham](https://github.com/guardrex) y [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) proporciona varios métodos de autorización que se pueden combinar con ASP.NET Core Identity :

* Grupos definidos por el usuario
  * Seguridad
  * O365
  * Distribución
* Roles
  * Roles administrativos integrados
  * Roles definidos por el usuario

Las instrucciones de este artículo se aplican a los Blazor escenarios de implementación de AAD de Webassembly que se describen en los temas siguientes:

* [Independiente con cuentas de Microsoft](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [Independiente con AAD](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [Hospedado con AAD](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Grupos definidos por el usuario y roles administrativos integrados

Para configurar la aplicación en el Azure Portal para proporcionar una `groups` demanda de pertenencia, consulte los siguientes artículos de Azure. Asigne usuarios a grupos de AAD definidos por el usuario y roles administrativos integrados.

* [Roles que usan grupos de seguridad de Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [Atributo groupMembershipClaims](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

En los ejemplos siguientes se supone que un usuario está asignado al rol de *Administrador de facturación* integrado de AAD.

La única `groups` demanda enviada por AAD presenta los grupos y roles del usuario como identificadores de objeto (GUID) en una matriz JSON. La aplicación debe convertir la matriz JSON de grupos y roles en `group` notificaciones individuales con las que la aplicación puede crear [directivas](xref:security/authorization/policies) .

Extender <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> para incluir las propiedades de la matriz para grupos y roles.

*CustomUserAccount.CS*:

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

Cree un generador de usuario personalizado en la aplicación independiente o la aplicación cliente de una solución hospedada. El siguiente generador también está configurado para controlar `roles` matrices de notificaciones, que se tratan en la sección [roles definidos por el usuario](#user-defined-roles) :

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

No es necesario proporcionar código para quitar la declaración original, `groups` ya que el marco de trabajo la quita automáticamente.

Registre el generador en `Program.Main` (*Program.CS*) de la aplicación independiente o la aplicación cliente de una solución hospedada:

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

Cree una [Directiva](xref:security/authorization/policies) para cada grupo o rol en `Program.Main` . En el ejemplo siguiente se crea una directiva para el rol de *Administrador de facturación* integrado de AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Para obtener una lista completa de los identificadores de objeto de rol de AAD, consulte la sección [identificadores de grupo de roles de AAD](#aad-adminstrative-role-group-ids) .

En los ejemplos siguientes, la aplicación usa la directiva anterior para autorizar al usuario.

El [componente AuthorizeView](xref:security/blazor/index#authorizeview-component) funciona con la Directiva:

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

El acceso a un componente completo puede basarse en la Directiva mediante la Directiva [ `[Authorize]` ] Attribute] (XREF: Security/increíbles/index # Authorize-Attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Si el usuario no ha iniciado sesión, se le redirigirá a la página de inicio de sesión de AAD y, después, volverá al componente después de iniciar sesión.

También se puede realizar una comprobación [de directiva en el código con lógica de procedimientos](xref:security/blazor/index#procedural-logic):

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

### <a name="user-defined-roles"></a>Roles definidos por el usuario

Una aplicación registrada en AAD también puede configurarse para usar roles definidos por el usuario.

Para configurar la aplicación en el Azure Portal para proporcionar una `roles` solicitud de pertenencia, consulte [Cómo: agregar roles de aplicación en la aplicación y recibirlos en el token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) en la documentación de Azure.

En el ejemplo siguiente se da por supuesto que una aplicación está configurada con dos roles:

* `admin`
* `developer`

> [!NOTE]
> Aunque no puede asignar roles a grupos de seguridad sin una cuenta de Azure AD Premium, puede asignar usuarios a roles y recibir una `roles` demanda de usuarios con una cuenta de Azure estándar. Las instrucciones de esta sección no requieren una cuenta de Azure AD Premium.
>
> Se asignan varios roles en el Azure Portal al **_volver a agregar un usuario_** para cada asignación de roles adicional.

La única `roles` demanda enviada por AAD presenta los roles definidos por el usuario como `appRoles` `value` s en una matriz JSON. La aplicación debe convertir la matriz JSON de roles en `role` notificaciones individuales.

El `CustomUserFactory` valor que se muestra en la sección [grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles) está configurado para actuar en una `roles` demanda con un valor de matriz JSON. Agregue y registre `CustomUserFactory` en la aplicación independiente o la aplicación cliente de una solución hospedada, tal como se muestra en la sección [grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles) . No es necesario proporcionar código para quitar la declaración original, `roles` ya que el marco de trabajo la quita automáticamente.

En `Program.Main` la aplicación independiente o la aplicación cliente de una solución hospedada, especifique la demanda denominada " `role` " como la demanda de rol:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Los enfoques de autorización de componentes son funcionales en este momento. Cualquiera de los mecanismos de autorización de los componentes puede usar el `admin` rol para autorizar al usuario:

* [Componente AuthorizeView](xref:security/blazor/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` ] Attribute (Directiva)] (XREF: Security/increíbles/index # Authorize-Attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (Ejemplo: `@attribute [Authorize(Roles = "admin")]` )
* [Lógica de procedimiento](xref:security/blazor/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }` )

  Se admiten varias pruebas de rol:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>Identificadores de grupo de roles administrativos de AAD

Los identificadores de objeto presentados en la tabla siguiente se usan para crear [directivas para las](xref:security/authorization/policies) `group` notificaciones. Las directivas permiten a una aplicación autorizar a los usuarios para distintas actividades en una aplicación. Para obtener más información, consulte la sección [grupos definidos por el usuario y roles administrativos integrados de AAD](#user-defined-groups-and-built-in-administrative-roles) .

Rol administrativo de AAD | Id. de objeto
--- | ---
Administrador de aplicaciones | fa11557b-4f15-4ddd-85d5-313c7cd74047
Desarrollador de aplicaciones | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Administrador de autenticación | 02d110a1-96b1-419e-af87-746461b60ed7
Administrador de Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Administrador de Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Administrador de conjunto de claves de B2C IEF | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Administrador de directivas de IEF B2C | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Administrador de flujo de usuario B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Administrador de atributo de flujo de usuario B2C | dd0baca0-a535-48c1-b871-8431abe16452
Administrador de facturación | 69ff516a-b57d-4697-a429-9de4af7b5609
Administrador de aplicaciones en la nube | 250b5fe3-b553-458d-9a53-b782c13c34bf
Administrador de dispositivos en la nube | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Administrador de cumplimiento | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Administrador de datos de cumplimiento | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrador de acceso condicional | 8f71a611-137d-49af-87ad-e97f1fd5da76
Aprobador del acceso a la Caja de seguridad del cliente | c18d54a8-b13e-4954-A1A4-7deaf2e4f184
Administrador de análisis de escritorio | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Lectores de directorios | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Administrador de Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Administrador de Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAdministrador de proveedor externo | febfaeb4-e478-407a-b4b3-f4d9716618a2
Administrador global | a45ba61b-44db-462c-924b-3b2719152588
Lector global | f6903b21-6aba-4124-b44c-76671796b9d5
Administrador de grupos | 158b3e5a-d89d-460b-92b5-3b34985f0197
Invitador de usuarios | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Administrador del departamento de soporte técnico | 108678c8-6628-44e1-8d01-caf598a6a5f5
Administrador de Intune | 79950741-23fa-4189-b2cb-46640601c497
Administrador de Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Administrador de licencias | 3355458a-e423-44bf-8b98-4ac5e572cea5
Lector de privacidad del Centro de mensajes | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Lector del centro de mensajes | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Administrador de aplicaciones de Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Administrador de contraseñas | 466e48b7-5d66-4ae5-8911-1a118de74941
Administrador de Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Administrador de Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Administrador de autenticación con privilegios | 0829f731-b46d-419f-9742-aeb122367d11
Administrador de roles con privilegios | f20a725a-d1c8-4107-83ea-1171c97d00c7
Lector de informes | 54635450-e8ed-4f2d-9632-07db2517b4de
Administrador de búsqueda | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor de búsqueda | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Administrador de seguridad | 20fa50e3-6531-44d8-bd39-b251420568ad
Operador de seguridad | 43aae017-8e51-4188-91ab-e6debd572800
Lector de seguridad | 45035cd3-fd97-4250-8197-3a53d3562d9b
Administrador del soporte técnico del servicio | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Administrador de SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Administrador de Skype Empresarial | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Administrador de comunicaciones de Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Ingeniero de soporte técnico de comunicaciones de Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Especialista en comunicaciones de equipos | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Administrador de servicios de Teams | 8846a0be-197b-443a-b13c-11192691fa24
Administrador de usuarios | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Recursos adicionales

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
