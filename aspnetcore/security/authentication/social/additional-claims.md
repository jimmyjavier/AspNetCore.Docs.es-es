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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="7c941-103">Conservar notificaciones y tokens adicionales de proveedores externos en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c941-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c941-104">Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter.</span><span class="sxs-lookup"><span data-stu-id="7c941-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="7c941-105">Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.</span><span class="sxs-lookup"><span data-stu-id="7c941-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="7c941-106">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c941-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c941-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7c941-107">Prerequisites</span></span>

<span data-ttu-id="7c941-108">Decida qué proveedores de autenticación externos admitir en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c941-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="7c941-109">Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="7c941-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="7c941-110">Para obtener más información, vea <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="7c941-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="7c941-111">La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="7c941-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="7c941-112">Establecimiento del identificador de cliente y el secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="7c941-112">Set the client ID and client secret</span></span>

<span data-ttu-id="7c941-113">El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="7c941-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="7c941-114">El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor.</span><span class="sxs-lookup"><span data-stu-id="7c941-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="7c941-115">Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor.</span><span class="sxs-lookup"><span data-stu-id="7c941-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="7c941-116">Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:</span><span class="sxs-lookup"><span data-stu-id="7c941-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="7c941-117">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="7c941-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7c941-118">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="7c941-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="7c941-119">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="7c941-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7c941-120">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="7c941-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7c941-121">Otros proveedores de autenticación</span><span class="sxs-lookup"><span data-stu-id="7c941-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="7c941-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="7c941-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="7c941-123">La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:</span><span class="sxs-lookup"><span data-stu-id="7c941-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="7c941-124">Establecer el ámbito de autenticación</span><span class="sxs-lookup"><span data-stu-id="7c941-124">Establish the authentication scope</span></span>

<span data-ttu-id="7c941-125">Especifique la lista de permisos que se van a recuperar del proveedor mediante la <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>especificación de.</span><span class="sxs-lookup"><span data-stu-id="7c941-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="7c941-126">Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="7c941-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="7c941-127">Proveedor</span><span class="sxs-lookup"><span data-stu-id="7c941-127">Provider</span></span>  | <span data-ttu-id="7c941-128">Ámbito</span><span class="sxs-lookup"><span data-stu-id="7c941-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="7c941-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="7c941-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="7c941-130">Google</span><span class="sxs-lookup"><span data-stu-id="7c941-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="7c941-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="7c941-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="7c941-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="7c941-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="7c941-133">En la aplicación de ejemplo, el `userinfo.profile` ámbito de Google lo agrega automáticamente el marco <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> de trabajo cuando se <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>llama a en.</span><span class="sxs-lookup"><span data-stu-id="7c941-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="7c941-134">Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones.</span><span class="sxs-lookup"><span data-stu-id="7c941-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="7c941-135">En el ejemplo siguiente, se agrega `https://www.googleapis.com/auth/user.birthday.read` el ámbito de Google para recuperar el cumpleaños de un usuario:</span><span class="sxs-lookup"><span data-stu-id="7c941-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="7c941-136">Asignar claves de datos de usuario y crear notificaciones</span><span class="sxs-lookup"><span data-stu-id="7c941-136">Map user data keys and create claims</span></span>

<span data-ttu-id="7c941-137">En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="7c941-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="7c941-138">Para obtener más información sobre los tipos de <xref:System.Security.Claims.ClaimTypes>notificaciones, vea.</span><span class="sxs-lookup"><span data-stu-id="7c941-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="7c941-139">La aplicación de ejemplo crea notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen ( `locale` ) `picture` a partir de las claves y en los datos de usuario de Google:</span><span class="sxs-lookup"><span data-stu-id="7c941-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="7c941-140">En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, se <xref:Microsoft.AspNetCore.Identity.IdentityUser> inicia`ApplicationUser`una sesión de () en la <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>aplicación con.</span><span class="sxs-lookup"><span data-stu-id="7c941-140">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="7c941-141">Durante el proceso de inicio de sesión <xref:Microsoft.AspNetCore.Identity.UserManager%601> , el puede `ApplicationUser` almacenar notificaciones para los <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>datos de usuario disponibles en.</span><span class="sxs-lookup"><span data-stu-id="7c941-141">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="7c941-142">En la aplicación de ejemplo `OnPostConfirmationAsync` , (*account/ExternalLogin. cshtml. CS*) establece las notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen () para `ApplicationUser`la sesión iniciada, <xref:System.Security.Claims.ClaimTypes.GivenName>incluida una notificación para:</span><span class="sxs-lookup"><span data-stu-id="7c941-142">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="7c941-143">De forma predeterminada, las notificaciones de un usuario se almacenan en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="7c941-143">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="7c941-144">Si la cookie de autenticación es demasiado grande, puede provocar un error en la aplicación porque:</span><span class="sxs-lookup"><span data-stu-id="7c941-144">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="7c941-145">El explorador detecta que el encabezado de la cookie es demasiado largo.</span><span class="sxs-lookup"><span data-stu-id="7c941-145">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="7c941-146">El tamaño total de la solicitud es demasiado grande.</span><span class="sxs-lookup"><span data-stu-id="7c941-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="7c941-147">Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:</span><span class="sxs-lookup"><span data-stu-id="7c941-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="7c941-148">Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c941-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="7c941-149">Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el middleware de autenticación de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> cookies para almacenar la identidad entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7c941-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="7c941-150">Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.</span><span class="sxs-lookup"><span data-stu-id="7c941-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="7c941-151">Guardar el token de acceso</span><span class="sxs-lookup"><span data-stu-id="7c941-151">Save the access token</span></span>

<span data-ttu-id="7c941-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define si los <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> tokens de acceso y de actualización deben almacenarse en después de una autorización correcta.</span><span class="sxs-lookup"><span data-stu-id="7c941-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="7c941-153">`SaveTokens`se establece en `false` de forma predeterminada para reducir el tamaño de la cookie de autenticación final.</span><span class="sxs-lookup"><span data-stu-id="7c941-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="7c941-154">La aplicación de ejemplo establece el valor `SaveTokens` de `true` en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>en:</span><span class="sxs-lookup"><span data-stu-id="7c941-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="7c941-155">Cuando `OnPostConfirmationAsync` se ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en `ApplicationUser`el `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="7c941-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="7c941-156">La aplicación de ejemplo guarda el token de `OnPostConfirmationAsync` acceso en (nuevo registro de `OnGetCallbackAsync` usuario) y (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="7c941-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="7c941-157">Cómo agregar tokens personalizados adicionales</span><span class="sxs-lookup"><span data-stu-id="7c941-157">How to add additional custom tokens</span></span>

<span data-ttu-id="7c941-158">Para mostrar cómo agregar un token personalizado, que se almacena como parte `SaveTokens`de, la aplicación de ejemplo agrega un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> elemento con el <xref:System.DateTime> actual para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="7c941-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="7c941-159">Creación y adición de notificaciones</span><span class="sxs-lookup"><span data-stu-id="7c941-159">Creating and adding claims</span></span>

<span data-ttu-id="7c941-160">El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección.</span><span class="sxs-lookup"><span data-stu-id="7c941-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="7c941-161">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="7c941-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="7c941-162">Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.</span><span class="sxs-lookup"><span data-stu-id="7c941-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="7c941-163">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="7c941-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="7c941-164">Eliminación de las notificaciones y las acciones de notificación</span><span class="sxs-lookup"><span data-stu-id="7c941-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="7c941-165">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> del especificado de la colección.</span><span class="sxs-lookup"><span data-stu-id="7c941-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="7c941-166">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad.</span><span class="sxs-lookup"><span data-stu-id="7c941-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="7c941-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.</span><span class="sxs-lookup"><span data-stu-id="7c941-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="7c941-168">Salida de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="7c941-168">Sample app output</span></span>

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

<span data-ttu-id="7c941-169">Una aplicación ASP.NET Core puede establecer notificaciones y tokens adicionales de proveedores de autenticación externos, como Facebook, Google, Microsoft y Twitter.</span><span class="sxs-lookup"><span data-stu-id="7c941-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="7c941-170">Cada proveedor revela información diferente sobre los usuarios en su plataforma, pero el patrón para recibir y transformar los datos de usuario en notificaciones adicionales es el mismo.</span><span class="sxs-lookup"><span data-stu-id="7c941-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="7c941-171">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c941-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c941-172">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="7c941-172">Prerequisites</span></span>

<span data-ttu-id="7c941-173">Decida qué proveedores de autenticación externos admitir en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c941-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="7c941-174">Para cada proveedor, registre la aplicación y obtenga un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="7c941-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="7c941-175">Para obtener más información, vea <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="7c941-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="7c941-176">La aplicación de ejemplo usa el [proveedor de autenticación de Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="7c941-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="7c941-177">Establecimiento del identificador de cliente y el secreto de cliente</span><span class="sxs-lookup"><span data-stu-id="7c941-177">Set the client ID and client secret</span></span>

<span data-ttu-id="7c941-178">El proveedor de autenticación OAuth establece una relación de confianza con una aplicación que usa un identificador de cliente y un secreto de cliente.</span><span class="sxs-lookup"><span data-stu-id="7c941-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="7c941-179">El proveedor de autenticación externo crea los valores de identificador de cliente y secreto de cliente para la aplicación cuando la aplicación se registra con el proveedor.</span><span class="sxs-lookup"><span data-stu-id="7c941-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="7c941-180">Cada proveedor externo que use la aplicación debe configurarse de forma independiente con el identificador de cliente y el secreto de cliente del proveedor.</span><span class="sxs-lookup"><span data-stu-id="7c941-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="7c941-181">Para obtener más información, consulte los temas de proveedores de autenticación externos que se aplican a su escenario:</span><span class="sxs-lookup"><span data-stu-id="7c941-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="7c941-182">Autenticación con Facebook</span><span class="sxs-lookup"><span data-stu-id="7c941-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="7c941-183">Autenticación con Google</span><span class="sxs-lookup"><span data-stu-id="7c941-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="7c941-184">Autenticación con Microsoft</span><span class="sxs-lookup"><span data-stu-id="7c941-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="7c941-185">Autenticación con Twitter</span><span class="sxs-lookup"><span data-stu-id="7c941-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="7c941-186">Otros proveedores de autenticación</span><span class="sxs-lookup"><span data-stu-id="7c941-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="7c941-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="7c941-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="7c941-188">La aplicación de ejemplo configura el proveedor de autenticación de Google con un identificador de cliente y un secreto de cliente proporcionado por Google:</span><span class="sxs-lookup"><span data-stu-id="7c941-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="7c941-189">Establecer el ámbito de autenticación</span><span class="sxs-lookup"><span data-stu-id="7c941-189">Establish the authentication scope</span></span>

<span data-ttu-id="7c941-190">Especifique la lista de permisos que se van a recuperar del proveedor mediante la <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>especificación de.</span><span class="sxs-lookup"><span data-stu-id="7c941-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="7c941-191">Los ámbitos de autenticación para proveedores externos comunes aparecen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="7c941-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="7c941-192">Proveedor</span><span class="sxs-lookup"><span data-stu-id="7c941-192">Provider</span></span>  | <span data-ttu-id="7c941-193">Ámbito</span><span class="sxs-lookup"><span data-stu-id="7c941-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="7c941-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="7c941-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="7c941-195">Google</span><span class="sxs-lookup"><span data-stu-id="7c941-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="7c941-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="7c941-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="7c941-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="7c941-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="7c941-198">En la aplicación de ejemplo, el `userinfo.profile` ámbito de Google lo agrega automáticamente el marco <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> de trabajo cuando se <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>llama a en.</span><span class="sxs-lookup"><span data-stu-id="7c941-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="7c941-199">Si la aplicación requiere ámbitos adicionales, agréguelos a las opciones.</span><span class="sxs-lookup"><span data-stu-id="7c941-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="7c941-200">En el ejemplo siguiente, se agrega `https://www.googleapis.com/auth/user.birthday.read` el ámbito de Google para recuperar el cumpleaños de un usuario:</span><span class="sxs-lookup"><span data-stu-id="7c941-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="7c941-201">Asignar claves de datos de usuario y crear notificaciones</span><span class="sxs-lookup"><span data-stu-id="7c941-201">Map user data keys and create claims</span></span>

<span data-ttu-id="7c941-202">En las opciones del proveedor, especifique <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> o <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> para cada clave o subclave de los datos de usuario JSON del proveedor externo para que la identidad de la aplicación Lea el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="7c941-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="7c941-203">Para obtener más información sobre los tipos de <xref:System.Security.Claims.ClaimTypes>notificaciones, vea.</span><span class="sxs-lookup"><span data-stu-id="7c941-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="7c941-204">La aplicación de ejemplo crea notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen ( `locale` ) `picture` a partir de las claves y en los datos de usuario de Google:</span><span class="sxs-lookup"><span data-stu-id="7c941-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="7c941-205">En `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, se <xref:Microsoft.AspNetCore.Identity.IdentityUser> inicia`ApplicationUser`una sesión de () en la <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>aplicación con.</span><span class="sxs-lookup"><span data-stu-id="7c941-205">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="7c941-206">Durante el proceso de inicio de sesión <xref:Microsoft.AspNetCore.Identity.UserManager%601> , el puede `ApplicationUser` almacenar notificaciones para los <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>datos de usuario disponibles en.</span><span class="sxs-lookup"><span data-stu-id="7c941-206">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="7c941-207">En la aplicación de ejemplo `OnPostConfirmationAsync` , (*account/ExternalLogin. cshtml. CS*) establece las notificaciones`urn:google:locale`de configuración regional (`urn:google:picture`) e imagen () para `ApplicationUser`la sesión iniciada, <xref:System.Security.Claims.ClaimTypes.GivenName>incluida una notificación para:</span><span class="sxs-lookup"><span data-stu-id="7c941-207">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="7c941-208">De forma predeterminada, las notificaciones de un usuario se almacenan en la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="7c941-208">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="7c941-209">Si la cookie de autenticación es demasiado grande, puede provocar un error en la aplicación porque:</span><span class="sxs-lookup"><span data-stu-id="7c941-209">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="7c941-210">El explorador detecta que el encabezado de la cookie es demasiado largo.</span><span class="sxs-lookup"><span data-stu-id="7c941-210">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="7c941-211">El tamaño total de la solicitud es demasiado grande.</span><span class="sxs-lookup"><span data-stu-id="7c941-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="7c941-212">Si se requiere una gran cantidad de datos de usuario para el procesamiento de solicitudes de usuario:</span><span class="sxs-lookup"><span data-stu-id="7c941-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="7c941-213">Limite el número y el tamaño de las notificaciones de usuario para el procesamiento de solicitudes solo a lo que requiere la aplicación.</span><span class="sxs-lookup"><span data-stu-id="7c941-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="7c941-214">Use un personalizado <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> para el middleware de autenticación de <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> cookies para almacenar la identidad entre las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="7c941-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="7c941-215">Conservar grandes cantidades de información de identidad en el servidor y enviar solo una pequeña clave de identificador de sesión al cliente.</span><span class="sxs-lookup"><span data-stu-id="7c941-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="7c941-216">Guardar el token de acceso</span><span class="sxs-lookup"><span data-stu-id="7c941-216">Save the access token</span></span>

<span data-ttu-id="7c941-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>define si los <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> tokens de acceso y de actualización deben almacenarse en después de una autorización correcta.</span><span class="sxs-lookup"><span data-stu-id="7c941-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="7c941-218">`SaveTokens`se establece en `false` de forma predeterminada para reducir el tamaño de la cookie de autenticación final.</span><span class="sxs-lookup"><span data-stu-id="7c941-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="7c941-219">La aplicación de ejemplo establece el valor `SaveTokens` de `true` en <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>en:</span><span class="sxs-lookup"><span data-stu-id="7c941-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="7c941-220">Cuando `OnPostConfirmationAsync` se ejecute, almacene el token de acceso ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) del proveedor externo en `ApplicationUser`el `AuthenticationProperties`.</span><span class="sxs-lookup"><span data-stu-id="7c941-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="7c941-221">La aplicación de ejemplo guarda el token de `OnPostConfirmationAsync` acceso en (nuevo registro de `OnGetCallbackAsync` usuario) y (usuario registrado previamente) en *account/ExternalLogin. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="7c941-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="7c941-222">Cómo agregar tokens personalizados adicionales</span><span class="sxs-lookup"><span data-stu-id="7c941-222">How to add additional custom tokens</span></span>

<span data-ttu-id="7c941-223">Para mostrar cómo agregar un token personalizado, que se almacena como parte `SaveTokens`de, la aplicación de ejemplo agrega un <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> elemento con el <xref:System.DateTime> actual para un [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) de `TicketCreated`:</span><span class="sxs-lookup"><span data-stu-id="7c941-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="7c941-224">Creación y adición de notificaciones</span><span class="sxs-lookup"><span data-stu-id="7c941-224">Creating and adding claims</span></span>

<span data-ttu-id="7c941-225">El marco de trabajo proporciona acciones comunes y métodos de extensión para crear y agregar notificaciones a la colección.</span><span class="sxs-lookup"><span data-stu-id="7c941-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="7c941-226">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> y <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="7c941-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="7c941-227">Los usuarios pueden definir acciones personalizadas derivando de <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> e implementando el <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> método abstracto.</span><span class="sxs-lookup"><span data-stu-id="7c941-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="7c941-228">Para obtener más información, vea <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="7c941-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="7c941-229">Eliminación de las notificaciones y las acciones de notificación</span><span class="sxs-lookup"><span data-stu-id="7c941-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="7c941-230">[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) quita todas las acciones de notificaciones <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> del especificado de la colección.</span><span class="sxs-lookup"><span data-stu-id="7c941-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="7c941-231">[ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) elimina una demanda del proporcionado <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> de la identidad.</span><span class="sxs-lookup"><span data-stu-id="7c941-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="7c941-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se usa principalmente con [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) para quitar notificaciones generadas por el protocolo.</span><span class="sxs-lookup"><span data-stu-id="7c941-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="7c941-233">Salida de la aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="7c941-233">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7c941-234">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="7c941-234">Additional resources</span></span>

* <span data-ttu-id="7c941-235">&ndash; [aplicación dotnet/AspNetCore Engineering SocialSample](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) la aplicación de ejemplo vinculada se encuentra en la rama de ingeniería `master` [del repositorio de github dotnet/AspNetCore](https://github.com/dotnet/AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="7c941-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="7c941-236">La `master` rama contiene código bajo desarrollo activo para la próxima versión de ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="7c941-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="7c941-237">Para ver una versión de la aplicación de ejemplo para una versión de lanzamiento de ASP.NET Core, use la lista desplegable **bifurcar** para seleccionar una rama de `release/{X.Y}`versión (por ejemplo,).</span><span class="sxs-lookup"><span data-stu-id="7c941-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
