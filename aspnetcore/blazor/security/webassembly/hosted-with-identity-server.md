---
title: Protección de una aplicación hospedada WebAssembly de Blazor de ASP.NET Core con Identity Server
author: guardrex
description: Crear en Visual Studio una aplicación hospedada Blazor con autenticación que usa un back-end de [IdentityServer](https://identityserver.io/)
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 8251658a2bb99ec17424ec26b8a44171082fab05
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243489"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Protección de una aplicación hospedada WebAssembly de Blazor de ASP.NET Core con Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

En este artículo se explica cómo crear una aplicación hospedada Blazor que usa [IdentityServer](https://identityserver.io/) para autenticar usuarios y llamadas API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En Visual Studio:

1. Cree una **aplicación WebAssembly de Blazor** . Para obtener más información, vea <xref:blazor/get-started>.
1. En el cuadro de diálogo **Crear una aplicación de Blazor** , seleccione **Cambiar** en la sección **Autenticación**.
1. Seleccione **Cuentas de usuario individuales** y, después, **Aceptar**.
1. Active la casilla **ASP.NET Core hosted** (Hospedada en ASP.NET Core) en la sección **Avanzado**.
1. Seleccione el botón **Crear**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

Para crear una aplicación usando un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar la ubicación de salida, lo que crea una carpeta de proyecto si no existe, incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample`). El nombre de la carpeta también pasa a formar parte del nombre del proyecto.

---

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

En las siguientes secciones se describen algunas adiciones al proyecto cuando se incluye compatibilidad con la autenticación.

### <a name="startup-class"></a>Clase Startup

La clase `Startup` tiene las siguientes adiciones.

* En `Startup.ConfigureServices`:

  * Identity de ASP.NET Core:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> adicional que configura convenciones de ASP.NET Core predeterminadas por encima de IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con un método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* En `Startup.Configure`:

  * El middleware IdentityServer expone los puntos de conexión de Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * El middleware Authentication es responsable de validar las credenciales de solicitud y de configurar el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El middleware Authorization habilita capacidades de autorización:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

El método auxiliar <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> configura [IdentityServer](https://identityserver.io/) en escenarios de ASP.NET Core. IdentityServer es un marco eficaz y extensible que sirve para abordar los problemas de seguridad de las aplicaciones. IdentityServer expone las complejidades innecesarias en los escenarios más comunes. En consecuencia, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida. Cuando sus necesidades de autenticación cambien, tendrá a su disposición toda la eficacia de IdentityServer para personalizar la autenticación para adaptarse a los requisitos de una aplicación.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

El método auxiliar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado. La directiva está configurada para permitir que Identity controle todas las solicitudes enrutadas a cualquier subruta en el espacio de dirección URL de Identity `/Identity`. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se encarga de todas las demás solicitudes. Este método también hace lo siguiente:

* Registra un recurso de API `{APPLICATION NAME}API` con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.
* Configura el middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) se aplica a la clase. Este atributo señala que, para acceder al recurso, el usuario debe estar autorizado en función de la directiva predeterminada. La directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> configura. El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado de las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer. La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Para obtener el control total del esquema de la base de datos, herede de una de las clases <xref:Microsoft.EntityFrameworkCore.DbContext> de Identity disponibles y configure el contexto para incluir el esquema de Identity llamando a `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el método <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), el punto de conexión del cliente se aprovisiona para proporcionar parámetros de OIDC.

### <a name="app-settings-files"></a>Archivo de configuración de la aplicación

En el archivo de configuración de la aplicación (`appsettings.json`), en la raíz del proyecto, la sección `IdentityServer` describe la lista de clientes configurados. En el siguiente ejemplo hay un solo cliente, cuyo nombre corresponde al nombre de la aplicación y se asigna por convención al parámetro `ClientId` de OAuth. El perfil señala el tipo de aplicación que se está configurando. El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

El marcador de posición `{APP ASSEMBLY}` es el nombre de ensamblado de la aplicación (por ejemplo, `BlazorSample.Client`).

## <a name="client-app-configuration"></a>Configuración de la aplicación cliente

### <a name="authentication-package"></a>Paquete de autenticación

Cuando una aplicación se crea para usar cuentas de usuario individuales (`Individual`), dicha aplicación recibe automáticamente una referencia de paquete del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar usuarios y a obtener tokens para llamar a API protegidas.

Si agrega autenticación a una aplicación, agregue el paquete manualmente al archivo de proyecto de la aplicación:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Compatibilidad con autorización de API

La compatibilidad para autenticar usuarios se incluye en el contenedor de servicios con el método de extensión proporcionado dentro del paquete [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/). Este método configura los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.

```csharp
builder.Services.AddApiAuthorization();
```

La configuración de la aplicación se carga por convención de forma predeterminada desde `_configuration/{client-id}`. Por convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación. Esta dirección URL se puede cambiar para que apunte a un punto de conexión aparte llamando a la sobrecarga con opciones.

### <a name="imports-file"></a>Archivo Imports

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente App

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

El componente `LoginDisplay` (`Shared/LoginDisplay.razor`) se representa en el componente `MainLayout` (`Shared/MainLayout.razor`) y administra los siguientes comportamientos:

* En el caso de los usuarios autenticados:
  * Muestra el nombre de usuario actual.
  * Proporciona un vínculo a la página de perfil de usuario en Identity de ASP.NET Core.
  * Proporciona un botón para cerrar la sesión de la aplicación.
* En el caso de los usuarios anónimos:
  * Ofrece la opción de registrarse.
  * Ofrece la opción de iniciar sesión.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Componente Authentication

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecutar la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, puede hacer lo siguiente:

* Establezca la lista desplegable **Proyectos de inicio** de la barra de herramientas en la *aplicación de API de servidor* y seleccione el botón **Ejecutar**.
* Seleccione el proyecto de servidor en el **Explorador de soluciones** y seleccione el botón **Ejecutar** de la barra de herramientas, o bien inicie la aplicación desde el menú **Depurar**.

## <a name="name-and-role-claim-with-api-authorization"></a>Notificaciones name y role con autorización de API

### <a name="custom-user-factory"></a>Fábrica de usuario personalizada

En la aplicación cliente, cree una fábrica de usuario personalizada. Identity Server envía varios roles como una matriz JSON en una sola notificación `role`. Se envía un único rol como un valor de cadena en la notificación. La fábrica crea una notificación `role` individual por cada uno de los roles del usuario.

`CustomUserFactory.cs`:

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

En la aplicación cliente, registre la fábrica en `Program.Main` (`Program.cs`):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

En la aplicación de servidor, llame a <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> en el generador de Identity, que agrega servicios relativos a roles:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Configuración de Identity Server

Siga **uno** de estos procedimientos:

* [Opciones de autorización de API](#api-authorization-options)
* [Servicio de perfil](#profile-service)

#### <a name="api-authorization-options"></a>Opciones de autorización de API

En la aplicación de servidor:

* Configure Identity Server para colocar las notificaciones `name` y `role` en el token de identificador y el token de acceso.
* Evite la asignación predeterminada de roles en el controlador de token JWT.

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a>Servicio de perfil

En la aplicación de servidor, cree una implementación `ProfileService`.

`ProfileService.cs`:

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

En la aplicación de servidor, registre el servicio de perfil en `Startup.ConfigureServices`:

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Uso de mecanismos de autorización

En la aplicación cliente, los métodos de autorización de componentes son funcionales en este momento. Cualquiera de los mecanismos de autorización de los componentes puede usar un rol para autorizar al usuario:

* [Componente `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">`)
* [Directiva de atributo `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (ejemplo: `@attribute [Authorize(Roles = "admin")]`)
* [Lógica de procedimientos](xref:blazor/security/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }`)

  Se admiten pruebas de rol múltiple:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name` se rellena en la aplicación cliente con el nombre de usuario del usuario, que suele ser su dirección de correo electrónico de inicio de sesión.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Implementación en Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importación de un certificado de Key Vault (documentación de Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Solicitudes de API web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
