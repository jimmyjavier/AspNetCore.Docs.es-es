---
title: Protección de WebAssembly de Blazor en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo proteger aplicaciones WebAssemlby de Blazor como aplicaciones de página única (SPA).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661789"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Protección de WebAssembly de Blazor en ASP.NET Core

Por [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Las aplicaciones WebAssembly de Blazor se protegen de la misma manera que las aplicaciones de página única (SPA). Hay varios métodos para autenticar a los usuarios en las SPA, pero el enfoque más común y completo consiste en usar una implementación basada en el [protocolo OAuth 2.0](https://oauth.net/), como [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteca de autenticación

WebAssembly de Blazor permite autenticar y autorizar aplicaciones mediante OIDC a través de la biblioteca `Microsoft.AspNetCore.Components.WebAssembly.Authentication`. La biblioteca proporciona un conjunto de primitivas para la autenticación sin problemas en back-ends de ASP.NET Core. La biblioteca integra ASP.NET Core Identity con compatibilidad con la autorización de API basada en [Identity Server](https://identityserver.io/). La biblioteca puede realizar la autenticación sobre cualquier proveedor de identidades (IP) de terceros que admita OIDC, que se denominan proveedores de OpenID (OP).

La compatibilidad con la autenticación en WebAssembly de Blazor se basa en la biblioteca *oidc-client.js*, que se usa para administrar los detalles del protocolo de autenticación subyacente.

Existen otras opciones para la autenticación de las SPA, como el uso de cookies de SameSite. Sin embargo, el diseño de ingeniería de WebAssembly de Blazor se limita a OAuth y OIDC como mejor opción para la autenticación en las aplicaciones WebAssembly de Blazor. Se ha elegido la [autenticación basada en tokens](xref:security/anti-request-forgery#token-based-authentication) basada en [JSON Web Token (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) antes que la [autenticación basada en cookies](xref:security/anti-request-forgery#cookie-based-authentication) por razones funcionales y de seguridad:

* El uso de un protocolo basado en tokens ofrece una superficie expuesta a ataques más pequeña, ya que los tokens no se envían en todas las solicitudes.
* Los puntos de conexión de servidor no requieren protección contra la [Falsificación de solicitudes entre sitios (CSRF)](xref:security/anti-request-forgery), ya que los tokens se envían de forma explícita. Esto permite hospedar aplicaciones WebAssembly de Blazor junto con aplicaciones MVC o Razor Pages.
* Los tokens tienen permisos más restringidos que las cookies. Por ejemplo, los tokens no se pueden usar para administrar la cuenta de usuario o cambiar su contraseña a menos que esa funcionalidad se implemente de forma explícita.
* Los tokens tienen una duración corta, una hora de forma predeterminada, lo que limita la ventana de ataque. Los tokens también se pueden revocar en cualquier momento.
* Los JWT independientes ofrecen garantías al cliente y al servidor sobre el proceso de autenticación. Por ejemplo, un cliente tiene los medios para detectar y validar que los tokens que recibe son legítimos y se han emitido como parte de un proceso de autenticación determinado. Si un tercero intenta cambiar un token en medio del proceso de autenticación, el cliente puede detectar el token cambiado y evitar usarlo.
* Los tokens con OAuth y OIDC no se basan en que el agente de usuario se comporte correctamente para asegurarse de que la aplicación sea segura.
* Los protocolos basados en tokens, como OAuth y OIDC, permiten autenticar y autorizar aplicaciones independientes y hospedadas con el mismo conjunto de características de seguridad.

## <a name="authentication-process-with-oidc"></a>Proceso de autenticación con OIDC

La biblioteca `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ofrece varias primitivas para implementar la autenticación y autorización mediante OIDC. En términos generales, la autenticación funciona de la siguiente manera:

* Cuando un usuario anónimo selecciona el botón de inicio de sesión o solicita una página con el atributo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) aplicado, se le redirige a la página de inicio de sesión de la aplicación (`/authentication/login`).
* En la página de inicio de sesión, la biblioteca de autenticación se prepara para un redireccionamiento al punto de conexión de autorización. El punto de conexión de autorización está fuera de la aplicación WebAssembly de Blazor y se puede hospedar en un origen independiente. El punto de conexión es responsable de determinar si el usuario está autenticado y de emitir uno o más tokens en respuesta. La biblioteca de autenticación proporciona una devolución de llamada de inicio de sesión para recibir la respuesta de autenticación.
  * Si el usuario no está autenticado, se le redirige al sistema de autenticación subyacente, que normalmente es ASP.NET Core Identity.
  * Si el usuario ya se ha autenticado, el punto de conexión de autorización genera los tokens adecuados y devuelve al explorador al punto de conexión de devolución de llamada de inicio de sesión (`/authentication/login-callback`).
* Cuando la aplicación WebAssembly de Blazor carga el punto de conexión de devolución de llamada de inicio de sesión (`/authentication/login-callback`), se procesa la respuesta de autenticación.
  * Si el proceso de autenticación se completa correctamente, el usuario se autentica y, opcionalmente, se devuelve a la dirección URL protegida original que haya solicitado.
  * Si por algún motivo se produce un error en el proceso de autenticación, se envía al usuario a la página de inicio de sesión con errores (`/authentication/login-failed`) y se muestra un error.

## <a name="support-prerendering-with-authentication"></a>Compatibilidad de la representación previa con la autenticación

Después de seguir las instrucciones que aparecen en uno de los temas de la aplicación WebAssembly de Blazor hospedada, use estas instrucciones para crear una aplicación que:

* Representa previamente las rutas de acceso para las que no se requiere autorización.
* No representa previamente las rutas de acceso para las que se requiere autorización.

En la clase `Program` de la aplicación cliente (*Program.cs*), factorice los registros de servicio comunes en un método independiente (por ejemplo, `ConfigureCommonServices`):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

En `Startup.ConfigureServices` de la aplicación de servidor, registre estos servicios adicionales:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

En el método `Startup.Configure` de la aplicación de servidor, reemplace `endpoints.MapFallbackToFile("index.html")` por `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

En la aplicación de servidor, cree una carpeta *Pages* si todavía no existe. Cree una página *_Host.cshtml* dentro de la carpeta *Pages* de la aplicación de servidor. Pegue el contenido del archivo *wwwroot/index.html* de la aplicación cliente en el archivo *Pages/_Host.cshtml*. Actualice el contenido del archivo:

* Agregue `@page "_Host"` a la parte superior del archivo.
* Reemplace la etiqueta `<app>Loading...</app>` por la siguiente:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opciones para aplicaciones hospedadas y proveedores de inicio de sesión de terceros

Al autenticar y autorizar una aplicación WebAssembly de Blazor con un proveedor de terceros, hay varias opciones disponibles para autenticar al usuario. Su elección depende del escenario.

Para obtener más información, vea <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticación de usuarios solo para llamadas a las API de terceros protegidas

Autentique al usuario con un flujo de OAuth del lado cliente en el proveedor de la API de terceros:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 En este escenario:

* El servidor que hospeda la aplicación no desempeña ningún rol.
* No se pueden proteger las API en el servidor.
* La aplicación solo puede llamar a las API de terceros protegidas.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticación de usuarios con un proveedor de terceros y llamada a las API protegidas en el servidor host y en el tercero

Configure Identity con un proveedor de inicio de sesión de terceros. Obtenga los tokens necesarios para el acceso de API de terceros y almacénelos.

Cuando un usuario inicia sesión, Identity recopila los tokens de acceso y actualización como parte del proceso de autenticación. En ese momento, hay un par de enfoques disponibles para realizar llamadas API a las API de terceros.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Uso de un token de acceso de servidor para recuperar el token de acceso de terceros

Use el token de acceso generado en el servidor para recuperar el token de acceso de terceros de un punto de conexión de la API de servidor. A partir de ahí, utilice el token de acceso de terceros para llamar directamente a los recursos de la API de terceros desde Identity en el cliente.

Este enfoque no se recomienda. Este enfoque requiere tratar el token de acceso de terceros como si se hubiera generado para un cliente público. En términos de OAuth, la aplicación pública no tiene un secreto de cliente porque no es de confianza para almacenar secretos de forma segura y el token de acceso se genera para un cliente confidencial. Un cliente confidencial es un cliente que tiene un secreto de cliente y se supone que puede almacenar secretos de forma segura.

* El token de acceso de terceros podría recibir ámbitos adicionales para realizar operaciones confidenciales en función del hecho de que la tercera parte emitió el token para un cliente de más confianza.
* Del mismo modo, los tokens de actualización no deben emitirse para un cliente que no sea de confianza, ya que esto proporciona acceso ilimitado al cliente a menos que se apliquen otras restricciones.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Realización de llamadas API desde el cliente a la API de servidor para llamar a las API de terceros

Realice una llamada API desde el cliente a la API del servidor. En el servidor, recupere el token de acceso para el recurso de la API de terceros y emita cualquier llamada que sea necesaria.

Aunque este enfoque requiere un salto de red adicional a través del servidor para llamar a una API de terceros, en última instancia resulta una experiencia más segura:

* El servidor puede almacenar tokens de actualización y asegurarse de que la aplicación no pierde el acceso a recursos de terceros.
* La aplicación no puede filtrar los tokens de acceso del servidor que puedan contener permisos más confidenciales.
