---
title: Compartir cookies de autenticación entre aplicaciones ASP.NET
author: rick-anderson
description: Aprenda a compartir cookies de autenticación entre ASP.NET 4. x y las aplicaciones ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: a6aac53008e634e87b18bd34b3d2babdad74ae50
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776147"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="6aa52-103">Compartir cookies de autenticación entre aplicaciones ASP.NET</span><span class="sxs-lookup"><span data-stu-id="6aa52-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="6aa52-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6aa52-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6aa52-105">Los sitios web a menudo se componen de aplicaciones web individuales que trabajan juntas.</span><span class="sxs-lookup"><span data-stu-id="6aa52-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="6aa52-106">Para proporcionar una experiencia de inicio de sesión único (SSO), las aplicaciones web dentro de un sitio deben compartir las cookies de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="6aa52-107">Para admitir este escenario, la pila de protección de datos permite compartir la autenticación de cookies de Katana y ASP.NET Core vales de autenticación de cookies.</span><span class="sxs-lookup"><span data-stu-id="6aa52-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="6aa52-108">En los ejemplos siguientes:</span><span class="sxs-lookup"><span data-stu-id="6aa52-108">In the examples that follow:</span></span>

* <span data-ttu-id="6aa52-109">El nombre de la cookie de autenticación se establece en un `.AspNet.SharedCookie`valor común de.</span><span class="sxs-lookup"><span data-stu-id="6aa52-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="6aa52-110">`AuthenticationType` Se establece en `Identity.Application` explícitamente o de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6aa52-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="6aa52-111">Se usa un nombre de aplicación común para habilitar el sistema de protección de datos para compartir las`SharedCookieApp`claves de protección de datos ().</span><span class="sxs-lookup"><span data-stu-id="6aa52-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="6aa52-112">`Identity.Application`se utiliza como esquema de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="6aa52-113">Sea cual sea el esquema que se use, se debe usar de forma coherente *dentro y entre* las aplicaciones de cookies compartidas, ya sea como esquema predeterminado o mediante su configuración explícita.</span><span class="sxs-lookup"><span data-stu-id="6aa52-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="6aa52-114">El esquema se usa al cifrar y descifrar las cookies, por lo que se debe usar un esquema coherente entre las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6aa52-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="6aa52-115">Se utiliza una ubicación de almacenamiento de [claves de protección de datos](xref:security/data-protection/implementation/key-management) común.</span><span class="sxs-lookup"><span data-stu-id="6aa52-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="6aa52-116">En ASP.NET Core aplicaciones, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> se usa para establecer la ubicación de almacenamiento de la clave.</span><span class="sxs-lookup"><span data-stu-id="6aa52-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="6aa52-117">En .NET Framework aplicaciones, el middleware de autenticación de cookies <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>usa una implementación de.</span><span class="sxs-lookup"><span data-stu-id="6aa52-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="6aa52-118">`DataProtectionProvider`proporciona servicios de protección de datos para el cifrado y descifrado de los datos de carga de la cookie de autenticación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="6aa52-119">La `DataProtectionProvider` instancia está aislada del sistema de protección de datos usado por otras partes de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="6aa52-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) acepta <xref:System.IO.DirectoryInfo> para especificar la ubicación del almacenamiento de la clave de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="6aa52-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="6aa52-121">`DataProtectionProvider`requiere el paquete NuGet [Microsoft. AspNetCore. bioprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="6aa52-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="6aa52-122">En ASP.NET Core aplicaciones 2. x, haga referencia al [metapaquete Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6aa52-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="6aa52-123">En .NET Framework aplicaciones, agregue una referencia de paquete a [Microsoft. AspNetCore. desproteccion. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="6aa52-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="6aa52-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>establece el nombre común de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="6aa52-125">Compartir cookies de autenticación con ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6aa52-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="6aa52-126">Al usar ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="6aa52-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="6aa52-127">Las claves de protección de datos y el nombre de la aplicación se deben compartir entre las aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6aa52-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="6aa52-128">Se proporciona una ubicación de almacenamiento de claves común <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> al método en los ejemplos siguientes.</span><span class="sxs-lookup"><span data-stu-id="6aa52-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="6aa52-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> para configurar un nombre común de aplicación compartida`SharedCookieApp` (en los ejemplos siguientes).</span><span class="sxs-lookup"><span data-stu-id="6aa52-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="6aa52-130">Para obtener más información, vea <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="6aa52-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="6aa52-131">Use el <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> método de extensión para configurar el servicio de protección de datos para las cookies.</span><span class="sxs-lookup"><span data-stu-id="6aa52-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="6aa52-132">El tipo de autenticación predeterminado `Identity.Application`es.</span><span class="sxs-lookup"><span data-stu-id="6aa52-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="6aa52-133">En `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6aa52-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="6aa52-134">Compartir cookies de autenticación sin ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6aa52-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="6aa52-135">Al usar cookies directamente sin IdentityASP.net Core, configure la protección de `Startup.ConfigureServices`datos y la autenticación en.</span><span class="sxs-lookup"><span data-stu-id="6aa52-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6aa52-136">En el ejemplo siguiente, el tipo de autenticación se establece `Identity.Application`en:</span><span class="sxs-lookup"><span data-stu-id="6aa52-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="6aa52-137">Compartir cookies entre diferentes rutas de acceso base</span><span class="sxs-lookup"><span data-stu-id="6aa52-137">Share cookies across different base paths</span></span>

<span data-ttu-id="6aa52-138">Una cookie de autenticación utiliza [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) como su [Cookie predeterminada. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="6aa52-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="6aa52-139">Si la cookie de la aplicación se debe compartir entre diferentes rutas de `Path` acceso base, se debe invalidar:</span><span class="sxs-lookup"><span data-stu-id="6aa52-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="6aa52-140">Compartir cookies entre subdominios</span><span class="sxs-lookup"><span data-stu-id="6aa52-140">Share cookies across subdomains</span></span>

<span data-ttu-id="6aa52-141">Cuando hospede aplicaciones que compartan cookies entre subdominios, especifique un dominio común en la propiedad [cookie. Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="6aa52-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="6aa52-142">Para compartir cookies entre aplicaciones `contoso.com`en, como `first_subdomain.contoso.com` y `second_subdomain.contoso.com`, especifique `Cookie.Domain` como `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="6aa52-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="6aa52-143">Cifrado de claves de protección de datos en reposo</span><span class="sxs-lookup"><span data-stu-id="6aa52-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="6aa52-144">En el caso de las implementaciones `DataProtectionProvider` de producción, configure el para cifrar las claves en reposo con DPAPI o un X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="6aa52-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="6aa52-145">Para obtener más información, vea <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="6aa52-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="6aa52-146">En el ejemplo siguiente, se proporciona una huella digital de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>certificado para:</span><span class="sxs-lookup"><span data-stu-id="6aa52-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="6aa52-147">Compartir cookies de autenticación entre ASP.NET 4. x y las aplicaciones ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6aa52-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="6aa52-148">Las aplicaciones ASP.NET 4. x que usan el middleware de autenticación de cookies Katana se pueden configurar para generar cookies de autenticación que son compatibles con el middleware de autenticación de cookies ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6aa52-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="6aa52-149">Esto permite actualizar las aplicaciones individuales de un sitio de gran tamaño en varios pasos, a la vez que proporciona una experiencia de SSO sin problemas en todo el sitio.</span><span class="sxs-lookup"><span data-stu-id="6aa52-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="6aa52-150">Cuando una aplicación usa el middleware de autenticación de cookies `UseCookieAuthentication` de Katana, llama a en el archivo *Startup.auth.CS* del proyecto.</span><span class="sxs-lookup"><span data-stu-id="6aa52-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="6aa52-151">Los proyectos de aplicación Web de ASP.NET 4. x creados con Visual Studio 2013 y versiones posteriores usan el middleware de autenticación de cookies Katana de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="6aa52-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="6aa52-152">Aunque `UseCookieAuthentication` está obsoleto y no se admite para las aplicaciones ASP.net Core, `UseCookieAuthentication` es válido llamar a en una aplicación ASP.net 4. x que usa el middleware de autenticación de cookies Katana.</span><span class="sxs-lookup"><span data-stu-id="6aa52-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="6aa52-153">Una aplicación ASP.NET 4. x debe tener como destino .NET Framework 4.5.1 o posterior.</span><span class="sxs-lookup"><span data-stu-id="6aa52-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="6aa52-154">De lo contrario, no se pueden instalar los paquetes de NuGet necesarios.</span><span class="sxs-lookup"><span data-stu-id="6aa52-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="6aa52-155">Para compartir las cookies de autenticación entre una aplicación ASP.NET 4. x y una aplicación ASP.NET Core, configure la aplicación de ASP.NET Core como se indica en la sección [compartir cookies de autenticación entre ASP.net Core aplicaciones](#share-authentication-cookies-with-aspnet-core-identity) y, a continuación, configure la aplicación ASP.net 4. x como se indica a continuación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="6aa52-156">Confirme que los paquetes de la aplicación se actualizan a las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="6aa52-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="6aa52-157">Instale el paquete [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) en cada aplicación ASP.net 4. x.</span><span class="sxs-lookup"><span data-stu-id="6aa52-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="6aa52-158">Busque y modifique la llamada a `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="6aa52-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="6aa52-159">Cambie el nombre de la cookie para que coincida con el nombre usado por el middleware`.AspNet.SharedCookie` de autenticación de cookies ASP.net Core (en el ejemplo).</span><span class="sxs-lookup"><span data-stu-id="6aa52-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="6aa52-160">En el ejemplo siguiente, el tipo de autenticación se establece `Identity.Application`en.</span><span class="sxs-lookup"><span data-stu-id="6aa52-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="6aa52-161">Proporcione una instancia de una `DataProtectionProvider` inicializada en la ubicación de almacenamiento de la clave de protección de datos común.</span><span class="sxs-lookup"><span data-stu-id="6aa52-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="6aa52-162">Confirme que el nombre de la aplicación se establece en el nombre de aplicación común que usan todas las aplicaciones que`SharedCookieApp` comparten cookies de autenticación (en el ejemplo).</span><span class="sxs-lookup"><span data-stu-id="6aa52-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="6aa52-163">Si no se `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` establece `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`y, <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> se establece en una demanda que distingue a usuarios únicos.</span><span class="sxs-lookup"><span data-stu-id="6aa52-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="6aa52-164">*App_Start/startup.auth.CS*:</span><span class="sxs-lookup"><span data-stu-id="6aa52-164">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="6aa52-165">Al generar una identidad de usuario, el tipo de`Identity.Application`autenticación () debe coincidir con `AuthenticationType` el tipo `UseCookieAuthentication` definido en establecido con en *App_Start/startup.auth.CS*.</span><span class="sxs-lookup"><span data-stu-id="6aa52-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="6aa52-166">*Modelos/IdentityModels. CS*:</span><span class="sxs-lookup"><span data-stu-id="6aa52-166">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="6aa52-167">Usar una base de datos de usuario común</span><span class="sxs-lookup"><span data-stu-id="6aa52-167">Use a common user database</span></span>

<span data-ttu-id="6aa52-168">Cuando las aplicaciones usen el Identity mismo esquema (la misma Identityversión de), confirme Identity que el sistema de cada aplicación señala a la misma base de datos de usuario.</span><span class="sxs-lookup"><span data-stu-id="6aa52-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="6aa52-169">De lo contrario, el sistema de identidad produce errores en tiempo de ejecución cuando intenta hacer coincidir la información de la cookie de autenticación con la información de su base de datos.</span><span class="sxs-lookup"><span data-stu-id="6aa52-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="6aa52-170">Cuando el Identity esquema es diferente entre las aplicaciones, normalmente porque las aplicaciones usan Identity versiones diferentes, compartir una base de datos común basada en la Identity versión más reciente de no es posible sin volver a asignar y Identity Agregar columnas en los esquemas de otra aplicación.</span><span class="sxs-lookup"><span data-stu-id="6aa52-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="6aa52-171">A menudo es más eficaz actualizar las otras aplicaciones para que usen la versión Identity más reciente para que las aplicaciones puedan compartir una base de datos común.</span><span class="sxs-lookup"><span data-stu-id="6aa52-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6aa52-172">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="6aa52-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
