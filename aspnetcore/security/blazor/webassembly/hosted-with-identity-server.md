---
title: Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Identity Server
author: guardrex
description: Para crear Blazor una nueva aplicación hospedada con autenticación desde Visual Studio que usa un back-end [de IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791616"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Proteja una Blazor aplicación hospedada de ASP.NET Core WebAssembly con Identity Server

Por [Javier Calvarro Nelson](https://github.com/javiercn) y Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Para crear Blazor una nueva aplicación hospedada en Visual Studio que use [IdentityServer](https://identityserver.io/) para autenticar usuarios y llamadas a la API:

1. Use Visual Studio para ** Blazor ** crear una nueva aplicación WebAssembly. Para obtener más información, vea <xref:blazor/get-started>.
1. En el cuadro de diálogo **Crear una Blazor nueva aplicación,** seleccione **Cambiar** en la sección **Autenticación.**
1. Seleccione **Cuentas de usuario individuales seguidas** de **Aceptar**.
1. Seleccione la casilla de verificación **ASP.NET Core hosted** en la sección **Avanzadas.**
1. Seleccione el botón **Crear**.

Para crear la aplicación en un shell de comandos, ejecute el siguiente comando:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Para especificar la ubicación de salida, que crea una carpeta de proyecto si no existe, `-o BlazorSample`incluya la opción de salida en el comando con una ruta de acceso (por ejemplo, ). El nombre de la carpeta también pasa a formar parte del nombre del proyecto.

## <a name="server-app-configuration"></a>Configuración de la aplicación de servidor

En las secciones siguientes se describen las adiciones al proyecto cuando se incluye la compatibilidad con la autenticación.

### <a name="startup-class"></a>Clase Startup

La `Startup` clase tiene las siguientes adiciones:

* En `Startup.ConfigureServices`:

  * Identidad:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer con <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> un método auxiliar adicional que configura algunas convenciones de ASP.NET Core predeterminadas sobre IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Autenticación con <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> un método auxiliar adicional que configura la aplicación para validar tokens JWT generados por IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* En `Startup.Configure`:

  * El middleware de autenticación que es responsable de validar las credenciales de la solicitud y establecer el usuario en el contexto de la solicitud:

    ```csharp
    app.UseAuthentication();
    ```

  * El middleware IdentityServer que expone los extremos de Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

El <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> método auxiliar configura [IdentityServer](https://identityserver.io/) para escenarios de ASP.NET Core. IdentityServer es un marco eficaz y extensible para controlar los problemas de seguridad de las aplicaciones. IdentityServer expone complejidad innecesaria para los escenarios más comunes. Por lo tanto, se proporciona un conjunto de convenciones y opciones de configuración que consideramos un buen punto de partida. Una vez que las necesidades de autenticación cambian, la potencia completa de IdentityServer todavía está disponible para personalizar la autenticación para adaptarse a los requisitos de una aplicación.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

El <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> método auxiliar configura un esquema de directiva según la aplicación como controlador de autenticación predeterminado. La directiva está configurada para permitir que la identidad controle todas `/Identity`las solicitudes enrutadas a cualquier subruta en el espacio DE URL de identidad. El <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> controla todas las demás solicitudes. Además, este método:

* Registra un `{APPLICATION NAME}API` recurso de API con IdentityServer con un ámbito predeterminado de `{APPLICATION NAME}API`.
* Configura el Middleware de token de portador de JWT para validar los tokens emitidos por IdentityServer para la aplicación.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

En `WeatherForecastController` el (*Controllers/WeatherForecastController.cs*), el [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo se aplica a la clase. El atributo indica que el usuario debe estar autorizado en función de la directiva predeterminada para tener acceso al recurso. La directiva de autorización predeterminada está configurada para utilizar <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> el esquema de autenticación predeterminado, que se configura mediante el esquema de directivas que se mencionó anteriormente. El método auxiliar <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> se configura como el controlador predeterminado para las solicitudes a la aplicación.

### <a name="applicationdbcontext"></a>ApplicationDbContext

En `ApplicationDbContext` el (*Data/ApplicationDbContext.cs*), lo mismo <xref:Microsoft.EntityFrameworkCore.DbContext> se utiliza <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> en Identity con la excepción que se extiende para incluir el esquema para IdentityServer. La clase <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> se deriva de la clase <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Para obtener el control total del esquema de <xref:Microsoft.EntityFrameworkCore.DbContext> base de datos, herede de `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` una `OnModelCreating` de las clases de identidad disponibles y configure el contexto para incluir el esquema de identidad mediante una llamada en el método.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

En `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), el extremo de cliente se aprovisiona para servir parámetros OIDC.

### <a name="app-settings-files"></a>Archivos de configuración de la aplicación

En el archivo de configuración de la aplicación ( `IdentityServer` *appsettings.json*) en la raíz del proyecto, en la sección se describe la lista de clientes configurados. En el ejemplo siguiente, hay un único cliente. El nombre del cliente corresponde al nombre de la `ClientId` aplicación y se asigna por convención al parámetro OAuth. El perfil indica el tipo de aplicación que se está configurando. El perfil se utiliza internamente para impulsar convenciones que simplifican el proceso de configuración del servidor. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

En el archivo de configuración de la aplicación de entorno de desarrollo *(appsettings. Development.json*) en la `IdentityServer` raíz del proyecto, en la sección se describe la clave utilizada para firmar tokens. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Configuración de la aplicación cliente

### <a name="authentication-package"></a>Paquete de autenticación

Cuando se crea una aplicación para`Individual`usar cuentas de usuario individuales ( ), la aplicación recibe automáticamente una referencia de paquete para el `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paquete en el archivo de proyecto de la aplicación. El paquete proporciona un conjunto de primitivas que ayudan a la aplicación a autenticar a los usuarios y obtener tokens para llamar a las API protegidas.

Si agrega autenticación a una aplicación, agregue manualmente el paquete al archivo de proyecto de la aplicación:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Reemplace `{VERSION}` en la referencia del paquete `Microsoft.AspNetCore.Blazor.Templates` anterior por <xref:blazor/get-started> la versión del paquete que se muestra en el artículo.

### <a name="api-authorization-support"></a>Soporte de autorización de API

La compatibilidad para autenticar usuarios se conecta al contenedor de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` servicios mediante el método de extensión proporcionado dentro del paquete. Este método configura todos los servicios necesarios para que la aplicación interactúe con el sistema de autorización existente.

```csharp
builder.Services.AddApiAuthorization();
```

De forma predeterminada, carga la configuración `_configuration/{client-id}`de la aplicación por convención desde . Por convención, el identificador de cliente se establece en el nombre del ensamblado de la aplicación. Esta dirección URL se puede cambiar para que apunte a un punto de conexión independiente llamando a la sobrecarga con opciones.

### <a name="imports-file"></a>Archivo de importaciones

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Página de índice

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Componente de la aplicación

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Componente RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Componente LoginDisplay

El `LoginDisplay` componente (*Shared/LoginDisplay.razor*) `MainLayout` se representa en el componente (*Shared/MainLayout.razor*) y administra los siguientes comportamientos:

* Para usuarios autenticados:
  * Muestra el nombre de usuario actual.
  * Ofrece un enlace a la página de perfil de usuario en ASP.NET Identidad principal.
  * Ofrece un botón para cerrar sesión en la aplicación.
* Para usuarios anónimos:
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

### <a name="authentication-component"></a>Componente de autenticación

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Componente FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ejecución la aplicación

Ejecute la aplicación desde el proyecto Servidor. Al usar Visual Studio, seleccione el proyecto de servidor en el **Explorador** de soluciones y seleccione el botón **Ejecutar** en la barra de herramientas o inicie la aplicación desde el menú **Depurar.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Recursos adicionales

* [Solicitar tokens de acceso adicionales](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
