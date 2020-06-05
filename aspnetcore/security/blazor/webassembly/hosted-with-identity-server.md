---
title: Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Identity Server
author: guardrex
description: Para crear una nueva Blazor aplicación hospedada con autenticación desde dentro de Visual Studio que usa un back-end de [IdentityServer](https://identityserver.io/)
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
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ade2d88c6a2d59e169c9019e871982a74ae46b33
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452322"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Protección de una Blazor aplicación hospedada en Webassembly ASP.net Core con Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) y [Luke Latham](https://github.com/guardrex)

En este artículo se explica cómo crear una nueva Blazor aplicación hospedada que usa [IdentityServer](https://identityserver.io/) para autenticar a los usuarios y las llamadas API.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

En Visual Studio:

1. Cree una nueva aplicación ** Blazor webassembly** . Para obtener más información, vea <xref:blazor/get-started>.
1. En el cuadro de diálogo **crear una nueva Blazor aplicación** , seleccione **cambiar** en la sección **autenticación** .
1. Seleccione **cuentas de usuario individuales** seguidos de **Aceptar**.
1. Active la casilla **ASP.net Core hospedado** en la sección **avanzadas** .
1. Seleccione el botón **Crear**.

# <a name="net-core-cli"></a>[CLI de .NET Core](#tab/netcore-cli/)

Para crear la aplicación en un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, incluya la opción output en el comando con una ruta de acceso (por ejemplo, `-o BlazorSample` ). El nombre de la carpeta también se convierte en parte del nombre del proyecto.

---

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

En las secciones siguientes se describen las adiciones al proyecto cuando se incluye compatibilidad con la autenticación.

### <a name="startup-class"></a>Clase Startup

La `Startup` clase tiene las siguientes adiciones.

* En `Startup.ConfigureServices`:

  * ASP.NET Core Identity :

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con un <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar adicional que configura convenciones de ASP.net Core predeterminadas en la parte superior de IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con un <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar adicional que configura la aplicación para validar los tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* En `Startup.Configure`:

  * El middleware IdentityServer expone los puntos de conexión de Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * El middleware de autenticación es responsable de validar las credenciales de solicitud y de configurar el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El middleware de autorización habilita las capacidades de autorización:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

El <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para escenarios ASP.net Core. IdentityServer es un marco de trabajo eficaz y extensible para controlar los problemas de seguridad de las aplicaciones. IdentityServer expone una complejidad innecesaria para los escenarios más comunes. Por lo tanto, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida. Una vez que cambie la autenticación, toda la capacidad de IdentityServer está disponible para personalizar la autenticación y adaptarse a los requisitos de una aplicación.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

El <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura un esquema de directiva para la aplicación como el controlador de autenticación predeterminado. La Directiva está configurada para permitir Identity que controle todas las solicitudes enrutadas a cualquier subruta de acceso en el espacio de la Identity dirección URL `/Identity` . <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Controla todas las demás solicitudes. Además, este método:

* Registra un `{APPLICATION NAME}API` recurso de API con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API` .
* Configura el middleware del token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En `WeatherForecastController` (*Controllers/WeatherForecastController. CS*), el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo se aplica a la clase. El atributo indica que el usuario debe estar autorizado en función de la directiva predeterminada para tener acceso al recurso. La Directiva de autorización predeterminada está configurada para usar el esquema de autenticación predeterminado, que se configura mediante <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> . El método auxiliar configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> como el controlador predeterminado para las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En `ApplicationDbContext` (*Data/ApplicationDbContext. CS*), <xref:Microsoft.EntityFrameworkCore.DbContext> se extiende <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> para incluir el esquema de IdentityServer. La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Para obtener el control total del esquema de la base de datos, herede de una de las Identity <xref:Microsoft.EntityFrameworkCore.DbContext> clases disponibles y configure el contexto para incluir el esquema llamando a Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` en el <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En `OidcConfigurationController` (*Controllers/OidcConfigurationController. CS*), el punto de conexión del cliente se aprovisiona para servir los parámetros de OIDC.

### <a name="app-settings-files"></a>Archivos de configuración de la aplicación

En el archivo de configuración de la aplicación (*appSettings. JSON*) en la raíz del proyecto, en la `IdentityServer` sección se describe la lista de clientes configurados. En el ejemplo siguiente, hay un solo cliente. El nombre de cliente se corresponde con el nombre de la aplicación y se asigna por Convención al parámetro de OAuth `ClientId` . El perfil indica el tipo de aplicación que se está configurando. El perfil se usa internamente para controlar las convenciones que simplifican el proceso de configuración del servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Configuración de la aplicación cliente

### <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para usar cuentas de usuario individuales ( `Individual` ), la aplicación recibe automáticamente una referencia de paquete para el paquete [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega la autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a>Compatibilidad con la autorización de API

La compatibilidad con la autenticación de usuarios está conectada en el contenedor de servicios mediante el método de extensión proporcionado en el paquete [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) . Este método configura los servicios que requiere la aplicación para interactuar con el sistema de autorización existente.

```csharp
builder.Services.AddApiAuthorization();
```

De forma predeterminada, la configuración de la aplicación se carga por Convención desde `_configuration/{client-id}` . Por Convención, el identificador de cliente se establece en el nombre de ensamblado de la aplicación. Esta dirección URL se puede cambiar para que apunte a un punto de conexión independiente mediante una llamada a la sobrecarga con opciones.

### <a name="imports-file"></a>Importar archivo

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente de aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

El `LoginDisplay` componente (*Shared/LoginDisplay. Razor*) se representa en el `MainLayout` componente (*Shared/MainLayout. Razor*) y administra los comportamientos siguientes:

* Para usuarios autenticados:
  * Muestra el nombre de usuario actual.
  * Proporciona un vínculo a la página de Perfil de usuario en ASP.NET Core Identity .
  * Ofrece un botón para cerrar la sesión de la aplicación.
* Para usuarios anónimos:
  * Ofrece la opción de registro.
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

### <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecución la aplicación

Ejecute la aplicación desde el proyecto de servidor. Al usar Visual Studio, puede:

* Establezca la lista desplegable **proyectos de inicio** en la barra de herramientas en la aplicación de *API de servidor* y seleccione el botón **Ejecutar** .
* Seleccione el proyecto de servidor en **Explorador de soluciones** y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación en el menú **depurar** .

## <a name="name-and-role-claim-with-api-authorization"></a>Nombre y notificaciones de rol con autorización de API

### <a name="custom-user-factory"></a>Generador de usuario personalizado

En la aplicación cliente, cree un generador de usuarios personalizado. IdentityEl servidor envía varios roles como una matriz JSON en una única `role` demanda. Un único rol se envía como un valor de cadena en la demanda. El generador crea una `role` demanda individual para cada uno de los roles del usuario.

*CustomUserFactory.CS*:

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

En la aplicación cliente, registre el generador en `Program.Main` (*Program.CS*):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

En la aplicación de servidor, llame a <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> en el Identity generador, que agrega servicios relacionados con roles:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a>Configurar Identity servidor

Siga **uno** de estos procedimientos:

* [Opciones de autorización de API](#api-authorization-options)
* [Servicio de perfiles](#profile-service)

#### <a name="api-authorization-options"></a>Opciones de autorización de API

En la aplicación de servidor:

* Configure Identity el servidor para colocar las `name` `role` notificaciones y en el token de identificador y el token de acceso.
* Evite la asignación predeterminada para roles en el controlador de token JWT.

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

#### <a name="profile-service"></a>Servicio de perfiles

En la aplicación de servidor, cree una `ProfileService` implementación de.

*ProfileService.CS*:

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

En la aplicación de servidor, registre el servicio de perfil en `Startup.ConfigureServices` :

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Usar mecanismos de autorización

En la aplicación cliente, los enfoques de autorización de componentes son funcionales en este momento. Cualquiera de los mecanismos de autorización de los componentes puede usar un rol para autorizar al usuario:

* [Componente AuthorizeView](xref:security/blazor/index#authorizeview-component) (ejemplo: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` Attribute](xref:security/blazor/index#authorize-attribute) (directiva <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) () (ejemplo: `@attribute [Authorize(Roles = "admin")]` )
* [Lógica de procedimiento](xref:security/blazor/index#procedural-logic) (ejemplo: `if (user.IsInRole("admin")) { ... }` )

  Se admiten varias pruebas de rol:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name`se rellena en la aplicación cliente con el nombre de usuario del usuario, que suele ser su dirección de correo electrónico de inicio de sesión.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Implementación en Azure App Service](xref:security/authentication/identity/spa#deploy-to-production)
* [Importar un certificado desde Key Vault (documentación de Azure)](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [Solicitudes de API Web no autenticadas o no autorizadas en una aplicación con un cliente predeterminado seguro](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
