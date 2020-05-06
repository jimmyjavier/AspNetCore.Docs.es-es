---
title: Conservar notificaciones y tokens adicionales de proveedores externos en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo establecer notificaciones y tokens adicionales de proveedores externos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 6acc1d78bf5cc39fd69329bad1cff0fbe52d9358
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769035"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Conservar notificaciones y tokens adicionales de proveedores externos en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter. Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

Decida qué proveedores de autenticación externos admitir en la aplicación. Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente. Para obtener más información, vea <xref:security/authentication/social/index>. La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Establecimiento del identificador de cliente y el secreto de cliente

El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente. El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor. Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor. Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:

* [Autenticación con Facebook](xref:security/authentication/facebook-logins)
* [Autenticación con Google](xref:security/authentication/google-logins)
* [Autenticación con Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticación con Twitter](xref:security/authentication/twitter-logins)
* [Otros proveedores de autenticación](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Establecer el ámbito de autenticación

Especifique la lista de permisos que se van a recuperar del proveedor mediante la <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>especificación de. Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.

| Proveedor  | Ámbito                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

En la aplicación de ejemplo, el `userinfo.profile` ámbito de Google lo agrega automáticamente el marco <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> de trabajo cuando se <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>llama a en. Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones. En el ejemplo siguiente, se agrega `https://www.googleapis.com/auth/user.birthday.read` el ámbito de Google para recuperar el cumpleaños de un usuario:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Asignar claves de datos de usuario y crear notificaciones

En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión. Para obtener más información sobre los tipos de <xref:System.Security.Claims.ClaimTypes>notificaciones, vea.

La aplicación de ejemplo crea notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen ( `locale` ) `picture` a partir de las claves y en los datos de usuario de Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, se <xref:Microsoft.AspNetCore.Identity.IdentityUser> inicia`ApplicationUser`una sesión de () en la <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>aplicación con. Durante el proceso de inicio de sesión <xref:Microsoft.AspNetCore.Identity.UserManager%601> , el puede `ApplicationUser` almacenar notificaciones para los <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>datos de usuario disponibles en.

En la aplicación de ejemplo `OnPostConfirmationAsync` , (*account/ExternalLogin. cshtml. CS*) establece las notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen () para `ApplicationUser`la sesión iniciada, <xref:System.Security.Claims.ClaimTypes.GivenName>incluida una notificación para:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

De forma predeterminada, las notificaciones de un usuario se almacenan en la cookie de autenticación. Si la cookie de autenticación es demasiado grande, puede provocar un error en la aplicación porque:

* El explorador detecta que el encabezado de la cookie es demasiado largo.
* El tamaño total de la solicitud es demasiado grande.

Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:

* Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.
* Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el middleware de autenticación de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> cookies para almacenar la identidad entre las solicitudes. Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.

## <a name="save-the-access-token"></a>Guardar el token de acceso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define si los <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> tokens de acceso y de actualización deben almacenarse en después de una autorización correcta. `SaveTokens`se establece en `false` de forma predeterminada para reducir el tamaño de la cookie de autenticación final.

La aplicación de ejemplo establece el valor `SaveTokens` de `true` en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>en:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Cuando `OnPostConfirmationAsync` se ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en `ApplicationUser`el `AuthenticationProperties`.

La aplicación de ejemplo guarda el token de `OnPostConfirmationAsync` acceso en (nuevo registro de `OnGetCallbackAsync` usuario) y (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Cómo agregar tokens personalizados adicionales

Para mostrar cómo agregar un token personalizado, que se almacena como parte `SaveTokens`de, la aplicación de ejemplo agrega un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> elemento con el <xref:System.DateTime> actual para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Creación y adición de notificaciones

El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección. Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.

Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Eliminación de las notificaciones y las acciones de notificación

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> del especificado de la colección. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.

## <a name="sample-app-output"></a>Salida de la aplicación de ejemplo

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter. Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Requisitos previos

Decida qué proveedores de autenticación externos admitir en la aplicación. Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente. Para obtener más información, vea <xref:security/authentication/social/index>. La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Establecimiento del identificador de cliente y el secreto de cliente

El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente. El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor. Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor. Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:

* [Autenticación con Facebook](xref:security/authentication/facebook-logins)
* [Autenticación con Google](xref:security/authentication/google-logins)
* [Autenticación con Microsoft](xref:security/authentication/microsoft-logins)
* [Autenticación con Twitter](xref:security/authentication/twitter-logins)
* [Otros proveedores de autenticación](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Establecer el ámbito de autenticación

Especifique la lista de permisos que se van a recuperar del proveedor mediante la <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>especificación de. Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.

| Proveedor  | Ámbito                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

En la aplicación de ejemplo, el `userinfo.profile` ámbito de Google lo agrega automáticamente el marco <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> de trabajo cuando se <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>llama a en. Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones. En el ejemplo siguiente, se agrega `https://www.googleapis.com/auth/user.birthday.read` el ámbito de Google para recuperar el cumpleaños de un usuario:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Asignar claves de datos de usuario y crear notificaciones

En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión. Para obtener más información sobre los tipos de <xref:System.Security.Claims.ClaimTypes>notificaciones, vea.

La aplicación de ejemplo crea notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen ( `locale` ) `picture` a partir de las claves y en los datos de usuario de Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, se <xref:Microsoft.AspNetCore.Identity.IdentityUser> inicia`ApplicationUser`una sesión de () en la <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>aplicación con. Durante el proceso de inicio de sesión <xref:Microsoft.AspNetCore.Identity.UserManager%601> , el puede `ApplicationUser` almacenar notificaciones para los <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>datos de usuario disponibles en.

En la aplicación de ejemplo `OnPostConfirmationAsync` , (*account/ExternalLogin. cshtml. CS*) establece las notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen () para `ApplicationUser`la sesión iniciada, <xref:System.Security.Claims.ClaimTypes.GivenName>incluida una notificación para:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

De forma predeterminada, las notificaciones de un usuario se almacenan en la cookie de autenticación. Si la cookie de autenticación es demasiado grande, puede provocar un error en la aplicación porque:

* El explorador detecta que el encabezado de la cookie es demasiado largo.
* El tamaño total de la solicitud es demasiado grande.

Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:

* Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.
* Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el middleware de autenticación de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> cookies para almacenar la identidad entre las solicitudes. Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.

## <a name="save-the-access-token"></a>Guardar el token de acceso

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define si los <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> tokens de acceso y de actualización deben almacenarse en después de una autorización correcta. `SaveTokens`se establece en `false` de forma predeterminada para reducir el tamaño de la cookie de autenticación final.

La aplicación de ejemplo establece el valor `SaveTokens` de `true` en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>en:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Cuando `OnPostConfirmationAsync` se ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en `ApplicationUser`el `AuthenticationProperties`.

La aplicación de ejemplo guarda el token de `OnPostConfirmationAsync` acceso en (nuevo registro de `OnGetCallbackAsync` usuario) y (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Cómo agregar tokens personalizados adicionales

Para mostrar cómo agregar un token personalizado, que se almacena como parte `SaveTokens`de, la aplicación de ejemplo agrega un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> elemento con el <xref:System.DateTime> actual para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Creación y adición de notificaciones

El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección. Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.

Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Eliminación de las notificaciones y las acciones de notificación

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> del especificado de la colección. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.

## <a name="sample-app-output"></a>Salida de la aplicación de ejemplo

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* &ndash; [aplicación dotnet/AspNetCore Engineering SocialSample](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) la aplicación de ejemplo vinculada se encuentra en la rama de ingeniería `master` [del repositorio de github dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) . La `master` rama contiene código bajo desarrollo activo para la próxima versión de ASP.net Core. Para ver una versión de la aplicación de ejemplo para una versión de lanzamiento de ASP.NET Core, use la lista desplegable **bifurcar** para seleccionar una rama de `release/{X.Y}`versión (por ejemplo,).
