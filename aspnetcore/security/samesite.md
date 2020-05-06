---
title: Trabajar con cookies de SameSite en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo usar para SameSite cookies en ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 43d5a3dbc5e202688e006355e0b105a86d721460
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775107"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="e970c-103">Trabajar con cookies de SameSite en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e970c-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="e970c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e970c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e970c-105">SameSite es un estándar de borrador de [IETF](https://ietf.org/about/) diseñado para proporcionar protección contra los ataques de falsificación de solicitud entre sitios (CSRF).</span><span class="sxs-lookup"><span data-stu-id="e970c-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="e970c-106">Originalmente elaborado en [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), el borrador estándar se actualizó en [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="e970c-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="e970c-107">El estándar actualizado no es compatible con versiones anteriores del estándar anterior, y las diferencias más destacables son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="e970c-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="e970c-108">Las cookies sin encabezado SameSite se `SameSite=Lax` tratan como de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e970c-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="e970c-109">`SameSite=None`debe usarse para permitir el uso de cookies entre sitios.</span><span class="sxs-lookup"><span data-stu-id="e970c-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="e970c-110">Las cookies `SameSite=None` que Assert también deben `Secure`marcarse como.</span><span class="sxs-lookup"><span data-stu-id="e970c-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="e970c-111">Las aplicaciones que [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) usan pueden experimentar problemas `sameSite=Lax` con `sameSite=Strict` las cookies o porque `<iframe>` se tratan como escenarios entre sitios.</span><span class="sxs-lookup"><span data-stu-id="e970c-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="e970c-112">El [estándar 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) no permite el valor `SameSite=Strict` `SameSite=None` y hace que algunas implementaciones traten estas cookies como.</span><span class="sxs-lookup"><span data-stu-id="e970c-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="e970c-113">Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e970c-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="e970c-114">La `SameSite=Lax` configuración funciona con la mayoría de las cookies de aplicación.</span><span class="sxs-lookup"><span data-stu-id="e970c-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="e970c-115">Algunas formas de autenticación como [OpenID Connect](https://openid.net/connect/) (OIDC) y [WS-Federation](https://auth0.com/docs/protocols/ws-fed) tienen como valor predeterminado el envío de redirecciones basadas en post.</span><span class="sxs-lookup"><span data-stu-id="e970c-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="e970c-116">Las redirecciones basadas en POST desencadenan las protecciones del explorador de SameSite, por lo que SameSite está deshabilitado para estos componentes.</span><span class="sxs-lookup"><span data-stu-id="e970c-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="e970c-117">La mayoría de los inicios de sesión de [OAuth](https://oauth.net/) no se ven afectados debido a las diferencias en el modo en que fluye la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e970c-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="e970c-118">Cada componente de ASP.NET Core que emite cookies debe decidir si SameSite es adecuado.</span><span class="sxs-lookup"><span data-stu-id="e970c-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="e970c-119">Código de ejemplo de prueba de SameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="e970c-120">Los ejemplos siguientes se pueden descargar y probar:</span><span class="sxs-lookup"><span data-stu-id="e970c-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="e970c-121">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e970c-121">Sample</span></span>               | <span data-ttu-id="e970c-122">Documento</span><span class="sxs-lookup"><span data-stu-id="e970c-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="e970c-123">MVC de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e970c-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="e970c-124">Razor Pages de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e970c-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e970c-125">El ejemplo siguiente se puede descargar y probar:</span><span class="sxs-lookup"><span data-stu-id="e970c-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="e970c-126">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e970c-126">Sample</span></span>               | <span data-ttu-id="e970c-127">Documento</span><span class="sxs-lookup"><span data-stu-id="e970c-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="e970c-128">Razor Pages de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e970c-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="e970c-129">Compatibilidad de .NET Core con el atributo sameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="e970c-130">.NET Core 2,2 admite el estándar de borrador 2019 para SameSite desde la publicación de actualizaciones en diciembre de 2019.</span><span class="sxs-lookup"><span data-stu-id="e970c-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="e970c-131">Los desarrolladores pueden controlar mediante programación el valor del atributo sameSite mediante la `HttpCookie.SameSite` propiedad.</span><span class="sxs-lookup"><span data-stu-id="e970c-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="e970c-132">Si se `SameSite` establece la propiedad en Strict, LAX o None, los valores se escriben en la red con la cookie.</span><span class="sxs-lookup"><span data-stu-id="e970c-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="e970c-133">Si el valor es igual a (SameSiteMode) (-1), indica que no debe incluirse ningún atributo sameSite en la red con la cookie.</span><span class="sxs-lookup"><span data-stu-id="e970c-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="e970c-134">.NET Core 3,0 admite los valores de SameSite actualizados y agrega un valor de enumeración `SameSiteMode.Unspecified` adicional `SameSiteMode` a la enumeración.</span><span class="sxs-lookup"><span data-stu-id="e970c-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="e970c-135">Este nuevo valor indica que no se debe enviar ningún sameSite con la cookie.</span><span class="sxs-lookup"><span data-stu-id="e970c-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="e970c-136">Cambios de comportamiento de revisión de diciembre</span><span class="sxs-lookup"><span data-stu-id="e970c-136">December patch behavior changes</span></span>

<span data-ttu-id="e970c-137">El cambio de comportamiento específico para .NET Framework y .NET Core 2,1 es el `SameSite` modo en que la `None` propiedad interpreta el valor.</span><span class="sxs-lookup"><span data-stu-id="e970c-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="e970c-138">Antes de que la revisión sea `None` un valor de pensado "no emitir el atributo en absoluto", después de la revisión significa "emitir el atributo con un `None`valor de".</span><span class="sxs-lookup"><span data-stu-id="e970c-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="e970c-139">Después de que la `SameSite` revisión sea `(SameSiteMode)(-1)` un valor de, no se emitirá el atributo.</span><span class="sxs-lookup"><span data-stu-id="e970c-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="e970c-140">El valor predeterminado de SameSite para las cookies de autenticación de formularios y `None` de `Lax`estado de sesión se cambió de a.</span><span class="sxs-lookup"><span data-stu-id="e970c-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="e970c-141">Uso de la API con SameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-141">API usage with SameSite</span></span>

<span data-ttu-id="e970c-142">[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) tiene `Unspecified`como valor predeterminado, lo que significa que no se ha agregado ningún atributo SameSite a la cookie y el cliente usará su comportamiento predeterminado (LAX para los nuevos exploradores, ninguno para los antiguos).</span><span class="sxs-lookup"><span data-stu-id="e970c-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="e970c-143">En el código siguiente se muestra cómo cambiar el valor de SameSite `SameSiteMode.Lax`de la cookie a:</span><span class="sxs-lookup"><span data-stu-id="e970c-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="e970c-144">Todos los componentes de ASP.NET Core que emiten cookies reemplazan a los valores predeterminados anteriores con la configuración adecuada para sus escenarios.</span><span class="sxs-lookup"><span data-stu-id="e970c-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="e970c-145">Los valores predeterminados anteriores invalidados no han cambiado.</span><span class="sxs-lookup"><span data-stu-id="e970c-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="e970c-146">Componente</span><span class="sxs-lookup"><span data-stu-id="e970c-146">Component</span></span> | <span data-ttu-id="e970c-147">cookie</span><span class="sxs-lookup"><span data-stu-id="e970c-147">cookie</span></span> | <span data-ttu-id="e970c-148">Default</span><span class="sxs-lookup"><span data-stu-id="e970c-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="e970c-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="e970c-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="e970c-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="e970c-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="e970c-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="e970c-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="e970c-152">Autenticación de cookies</span><span class="sxs-lookup"><span data-stu-id="e970c-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="e970c-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="e970c-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="e970c-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="e970c-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="e970c-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="e970c-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="e970c-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="e970c-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="e970c-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="e970c-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="e970c-158">ASP.NET Core 3,1 y versiones posteriores proporcionan la siguiente compatibilidad con SameSite:</span><span class="sxs-lookup"><span data-stu-id="e970c-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="e970c-159">Vuelve a definir el comportamiento de `SameSiteMode.None` para emitir.`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="e970c-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="e970c-160">Agrega un nuevo valor `SameSiteMode.Unspecified` para omitir el atributo SameSite.</span><span class="sxs-lookup"><span data-stu-id="e970c-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="e970c-161">Todas las API de `Unspecified`cookies tienen como valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="e970c-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="e970c-162">Algunos componentes que usan cookies establecen valores más específicos de sus escenarios.</span><span class="sxs-lookup"><span data-stu-id="e970c-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="e970c-163">Vea la tabla anterior para obtener ejemplos.</span><span class="sxs-lookup"><span data-stu-id="e970c-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e970c-164">En ASP.NET Core 3,0 y versiones posteriores, se cambiaron los valores predeterminados de SameSite para evitar conflictos con los valores predeterminados de cliente incoherentes.</span><span class="sxs-lookup"><span data-stu-id="e970c-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="e970c-165">Las siguientes API han cambiado el valor predeterminado `SameSiteMode.Lax ` de `-1` a para evitar la emisión de un atributo SameSite para estas cookies:</span><span class="sxs-lookup"><span data-stu-id="e970c-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="e970c-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>se usa con [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="e970c-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="e970c-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>se usa como generador para`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="e970c-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="e970c-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e970c-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="e970c-169">Historial y cambios</span><span class="sxs-lookup"><span data-stu-id="e970c-169">History and changes</span></span>

<span data-ttu-id="e970c-170">La compatibilidad con SameSite se implementó por primera vez en ASP.NET Core en 2,0 con el [borrador estándar de 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="e970c-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="e970c-171">El estándar 2016 era participación.</span><span class="sxs-lookup"><span data-stu-id="e970c-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="e970c-172">ASP.NET Core participar estableciendo varias cookies en `Lax` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e970c-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="e970c-173">Después de encontrar varios [problemas](https://github.com/aspnet/Announcements/issues/318) con la autenticación, se [deshabilitó](https://github.com/aspnet/Announcements/issues/348)la mayor parte del uso de SameSite.</span><span class="sxs-lookup"><span data-stu-id="e970c-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="e970c-174">Las [revisiones](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) se emitieron en noviembre de 2019 para actualizar desde el estándar 2016 al estándar 2019.</span><span class="sxs-lookup"><span data-stu-id="e970c-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="e970c-175">El [borrador 2019 de la especificación SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="e970c-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="e970c-176">**No** es compatible con las versiones anteriores del borrador 2016.</span><span class="sxs-lookup"><span data-stu-id="e970c-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="e970c-177">Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e970c-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="e970c-178">Especifica que `SameSite=Lax` las cookies se tratan de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e970c-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="e970c-179">Especifica las cookies que `SameSite=None` imponen explícitamente para permitir la entrega entre sitios debe `Secure`marcarse como.</span><span class="sxs-lookup"><span data-stu-id="e970c-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="e970c-180">`None`es una nueva entrada que se va a dejar de participar.</span><span class="sxs-lookup"><span data-stu-id="e970c-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="e970c-181">Es compatible con las revisiones emitidas para ASP.NET Core 2,1, 2,2 y 3,0.</span><span class="sxs-lookup"><span data-stu-id="e970c-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="e970c-182">ASP.NET Core 3,1 tiene compatibilidad adicional con SameSite.</span><span class="sxs-lookup"><span data-stu-id="e970c-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="e970c-183">Está programado para que [Chrome](https://chromestatus.com/feature/5088147346030592) lo habilite de forma predeterminada en [febrero de 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="e970c-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="e970c-184">Los exploradores empezaron a pasar a este estándar en 2019.</span><span class="sxs-lookup"><span data-stu-id="e970c-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="e970c-185">API afectadas por el cambio del estándar de borrador de 2016 SameSite al borrador estándar de 2019</span><span class="sxs-lookup"><span data-stu-id="e970c-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="e970c-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="e970c-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="e970c-187">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="e970c-188">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="e970c-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="e970c-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="e970c-190">Compatibilidad con exploradores más antiguos</span><span class="sxs-lookup"><span data-stu-id="e970c-190">Supporting older browsers</span></span>

<span data-ttu-id="e970c-191">El estándar 2016 SameSite impediba que los valores desconocidos se `SameSite=Strict` deben tratar como valores.</span><span class="sxs-lookup"><span data-stu-id="e970c-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="e970c-192">Las aplicaciones a las que se accede desde exploradores más antiguos que admiten el estándar 2016 SameSite se pueden interrumpir cuando obtienen `None`una propiedad SameSite con un valor de.</span><span class="sxs-lookup"><span data-stu-id="e970c-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="e970c-193">Las aplicaciones web deben implementar la detección del explorador si pretenden admitir exploradores más antiguos.</span><span class="sxs-lookup"><span data-stu-id="e970c-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="e970c-194">ASP.NET Core no implementa la detección del explorador porque los valores de los agentes de usuario son muy volátiles y cambian con frecuencia.</span><span class="sxs-lookup"><span data-stu-id="e970c-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="e970c-195">Un punto de extensión <xref:Microsoft.AspNetCore.CookiePolicy> de permite conectar una lógica específica del agente de usuario.</span><span class="sxs-lookup"><span data-stu-id="e970c-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="e970c-196">En `Startup.Configure`, agregue código que llame <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> a antes <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> de llamar a o a *cualquier* método que escriba cookies:</span><span class="sxs-lookup"><span data-stu-id="e970c-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="e970c-197">En `Startup.ConfigureServices`, agregue código similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e970c-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="e970c-198">En el ejemplo anterior, `MyUserAgentDetectionLib.DisallowsSameSiteNone` es una biblioteca suministrada por el usuario que detecta si el agente de usuario `None`no es compatible con SameSite:</span><span class="sxs-lookup"><span data-stu-id="e970c-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="e970c-199">En el código siguiente se muestra `DisallowsSameSiteNone` un método de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e970c-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="e970c-200">El código siguiente es solo para la demostración:</span><span class="sxs-lookup"><span data-stu-id="e970c-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="e970c-201">No debe considerarse como completa.</span><span class="sxs-lookup"><span data-stu-id="e970c-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="e970c-202">No se mantiene ni se admite.</span><span class="sxs-lookup"><span data-stu-id="e970c-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="e970c-203">Probar aplicaciones para problemas de SameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="e970c-204">Las aplicaciones que interactúan con sitios remotos, como a través de un inicio de sesión de terceros, deben:</span><span class="sxs-lookup"><span data-stu-id="e970c-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="e970c-205">Pruebe la interacción en varios exploradores.</span><span class="sxs-lookup"><span data-stu-id="e970c-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="e970c-206">Aplique la [detección y la mitigación del explorador de CookiePolicy](#sob) descritas en este documento.</span><span class="sxs-lookup"><span data-stu-id="e970c-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="e970c-207">Pruebe las aplicaciones web con una versión de cliente que pueda participar en el nuevo comportamiento de SameSite.</span><span class="sxs-lookup"><span data-stu-id="e970c-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="e970c-208">Chrome, Firefox y el borde de cromo tienen nuevas marcas de características opcionales que se pueden usar para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="e970c-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="e970c-209">Una vez que la aplicación aplica las revisiones de SameSite, probarla con versiones de cliente anteriores, especialmente Safari.</span><span class="sxs-lookup"><span data-stu-id="e970c-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="e970c-210">Para obtener más información, consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e970c-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="e970c-211">Prueba con Chrome</span><span class="sxs-lookup"><span data-stu-id="e970c-211">Test with Chrome</span></span>

<span data-ttu-id="e970c-212">Chrome 78 + ofrece resultados engañosos porque tiene una mitigación temporal en contexto.</span><span class="sxs-lookup"><span data-stu-id="e970c-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="e970c-213">La mitigación temporal de Chrome 78 + permite cookies con menos de dos minutos de antigüedad.</span><span class="sxs-lookup"><span data-stu-id="e970c-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="e970c-214">Chrome 76 o 77 con las marcas de prueba adecuadas habilitadas proporcionan resultados más precisos.</span><span class="sxs-lookup"><span data-stu-id="e970c-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="e970c-215">Para probar el nuevo comportamiento de SameSite, `chrome://flags/#same-site-by-default-cookies` cambie a **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="e970c-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="e970c-216">Las versiones anteriores de Chrome (75 e inferiores) se muestran como erróneas con `None` la nueva configuración.</span><span class="sxs-lookup"><span data-stu-id="e970c-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="e970c-217">Consulte [compatibilidad con exploradores anteriores](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e970c-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="e970c-218">Google no hace que las versiones anteriores de Chrome estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="e970c-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="e970c-219">Siga las instrucciones de [Descargar cromo](https://www.chromium.org/getting-involved/download-chromium) para probar versiones anteriores de Chrome.</span><span class="sxs-lookup"><span data-stu-id="e970c-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="e970c-220">**No** Descargue Chrome desde los vínculos que se proporcionan al buscar versiones anteriores de Chrome.</span><span class="sxs-lookup"><span data-stu-id="e970c-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="e970c-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="e970c-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="e970c-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="e970c-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="e970c-223">A partir de la `80.0.3975.0`versión Canarias, se puede deshabilitar la mitigación temporal más estricta y posterior a la hora `--enable-features=SameSiteDefaultChecksMethodRigorously` de realizar pruebas con la nueva marca para permitir la realización de pruebas de sitios y servicios en el estado final eventual de la característica en la que se ha quitado la mitigación.</span><span class="sxs-lookup"><span data-stu-id="e970c-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="e970c-224">Para obtener más información, consulte las actualizaciones de los proyectos de cromo [SameSite](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="e970c-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="e970c-225">Prueba con Safari</span><span class="sxs-lookup"><span data-stu-id="e970c-225">Test with Safari</span></span>

<span data-ttu-id="e970c-226">Safari 12 implementó estrictamente el borrador anterior y produce un `None` error cuando el nuevo valor está en una cookie.</span><span class="sxs-lookup"><span data-stu-id="e970c-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="e970c-227">`None`se evita a través del código de detección del explorador que [admite exploradores más antiguos](#sob) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e970c-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="e970c-228">Pruebe los inicios de sesión de estilo de SO basados en Safari 12, Safari 13 y WebKit con MSAL, ADAL o cualquier biblioteca que use.</span><span class="sxs-lookup"><span data-stu-id="e970c-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="e970c-229">El problema depende de la versión del sistema operativo subyacente.</span><span class="sxs-lookup"><span data-stu-id="e970c-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="e970c-230">OSX Mojave (10,14) e iOS 12 se sabe que tienen problemas de compatibilidad con el nuevo comportamiento de SameSite.</span><span class="sxs-lookup"><span data-stu-id="e970c-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="e970c-231">Al actualizar el sistema operativo a OSX Catalina (10,15) o iOS 13 se corrige el problema.</span><span class="sxs-lookup"><span data-stu-id="e970c-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="e970c-232">Safari no tiene actualmente una marca de participación para probar el nuevo comportamiento de la especificación.</span><span class="sxs-lookup"><span data-stu-id="e970c-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="e970c-233">Prueba con Firefox</span><span class="sxs-lookup"><span data-stu-id="e970c-233">Test with Firefox</span></span>

<span data-ttu-id="e970c-234">La compatibilidad con Firefox para el nuevo estándar se puede probar en la `about:config` versión 68 + al optar por la página con `network.cookie.sameSite.laxByDefault`la marca de la característica.</span><span class="sxs-lookup"><span data-stu-id="e970c-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="e970c-235">No ha habido informes de problemas de compatibilidad con versiones anteriores de Firefox.</span><span class="sxs-lookup"><span data-stu-id="e970c-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="e970c-236">Probar con el explorador Edge</span><span class="sxs-lookup"><span data-stu-id="e970c-236">Test with Edge browser</span></span>

<span data-ttu-id="e970c-237">Edge es compatible con el estándar SameSite antiguo.</span><span class="sxs-lookup"><span data-stu-id="e970c-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="e970c-238">La versión perimetral 44 no tiene ningún problema de compatibilidad conocido con el nuevo estándar.</span><span class="sxs-lookup"><span data-stu-id="e970c-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="e970c-239">Prueba con borde (cromo)</span><span class="sxs-lookup"><span data-stu-id="e970c-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="e970c-240">Las marcas SameSite se establecen en `edge://flags/#same-site-by-default-cookies` la página.</span><span class="sxs-lookup"><span data-stu-id="e970c-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="e970c-241">No se detectaron problemas de compatibilidad con el cromo perimetral.</span><span class="sxs-lookup"><span data-stu-id="e970c-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="e970c-242">Prueba con electrones</span><span class="sxs-lookup"><span data-stu-id="e970c-242">Test with Electron</span></span>

<span data-ttu-id="e970c-243">Las versiones de Electron incluyen versiones anteriores de Chromium.</span><span class="sxs-lookup"><span data-stu-id="e970c-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="e970c-244">Por ejemplo, la versión de electrones utilizada por los equipos es cromo 66, que exhibe el comportamiento anterior.</span><span class="sxs-lookup"><span data-stu-id="e970c-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="e970c-245">Debe realizar sus propias pruebas de compatibilidad con la versión de electrones que usa el producto.</span><span class="sxs-lookup"><span data-stu-id="e970c-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="e970c-246">Consulte [compatibilidad con exploradores anteriores](#sob) en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="e970c-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e970c-247">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e970c-247">Additional resources</span></span>

* [<span data-ttu-id="e970c-248">Blog de cromo: desarrolladores: Prepárese para New SameSite = None; Configuración de cookies seguras</span><span class="sxs-lookup"><span data-stu-id="e970c-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="e970c-249">Explicación de las cookies de SameSite</span><span class="sxs-lookup"><span data-stu-id="e970c-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="e970c-250">Revisiones de noviembre de 2019</span><span class="sxs-lookup"><span data-stu-id="e970c-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="e970c-251">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e970c-251">Sample</span></span>               | <span data-ttu-id="e970c-252">Documento</span><span class="sxs-lookup"><span data-stu-id="e970c-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="e970c-253">MVC de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e970c-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="e970c-254">Razor Pages de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e970c-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="e970c-255">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="e970c-255">Sample</span></span>               | <span data-ttu-id="e970c-256">Documento</span><span class="sxs-lookup"><span data-stu-id="e970c-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="e970c-257">[Páginas de Razor .net Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="e970c-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
