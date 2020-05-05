---
title: Habilitación de solicitudes entre orígenes (CORS) en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre cómo CORS como estándar para permitir o rechazar solicitudes entre orígenes en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773816"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="8b11f-103">Habilitación de solicitudes entre orígenes (CORS) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b11f-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b11f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="8b11f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="8b11f-105">En este artículo se muestra cómo habilitar CORS en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b11f-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="8b11f-106">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web.</span><span class="sxs-lookup"><span data-stu-id="8b11f-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8b11f-107">Esta restricción se denomina *directiva de mismo origen*.</span><span class="sxs-lookup"><span data-stu-id="8b11f-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="8b11f-108">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="8b11f-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8b11f-109">En ocasiones, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="8b11f-110">Para obtener más información, consulte el [artículo Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="8b11f-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="8b11f-111">[Uso compartido de recursos entre orígenes](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="8b11f-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="8b11f-112">Es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="8b11f-113">**No** es una característica de seguridad, CORS reduce la seguridad.</span><span class="sxs-lookup"><span data-stu-id="8b11f-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="8b11f-114">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="8b11f-115">Para obtener más información, vea [Cómo funciona CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="8b11f-116">Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras se rechazan otras.</span><span class="sxs-lookup"><span data-stu-id="8b11f-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="8b11f-117">Es más seguro y más flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="8b11f-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="8b11f-118">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b11f-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="8b11f-119">Mismo origen</span><span class="sxs-lookup"><span data-stu-id="8b11f-119">Same origin</span></span>

<span data-ttu-id="8b11f-120">Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="8b11f-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="8b11f-121">Estas dos direcciones URL tienen el mismo origen:</span><span class="sxs-lookup"><span data-stu-id="8b11f-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="8b11f-122">Estas direcciones URL tienen distintos orígenes que las dos direcciones URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="8b11f-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="8b11f-123">`https://example.net`&ndash; Dominio diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="8b11f-124">`https://www.example.com/foo.html`&ndash; Subdominio diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="8b11f-125">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="8b11f-126">`https://example.com:9000/foo.html`&ndash; Puerto diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="8b11f-127">Habilitación de CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-127">Enable CORS</span></span>

<span data-ttu-id="8b11f-128">Hay tres maneras de habilitar CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="8b11f-129">En middleware con una directiva [con nombre](#np) o una [directiva predeterminada](#dp).</span><span class="sxs-lookup"><span data-stu-id="8b11f-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="8b11f-130">Usar el [enrutamiento de puntos de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="8b11f-131">Con el atributo [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="8b11f-132">El uso del atributo [[EnableCors]](#attr) con una directiva con nombre proporciona el control más fino para limitar los puntos de conexión que admiten CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="8b11f-133">Cada enfoque se detalla en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="8b11f-134">CORS con Directiva con nombre y middleware</span><span class="sxs-lookup"><span data-stu-id="8b11f-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="8b11f-135">Middleware de CORS controla las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="8b11f-136">En el código siguiente se aplica una directiva de CORS a todos los puntos de conexión de la aplicación con los orígenes especificados:</span><span class="sxs-lookup"><span data-stu-id="8b11f-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="8b11f-137">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="8b11f-137">The preceding code:</span></span>

* <span data-ttu-id="8b11f-138">Establece el nombre de la `_myAllowSpecificOrigins`Directiva en.</span><span class="sxs-lookup"><span data-stu-id="8b11f-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="8b11f-139">El nombre de la Directiva es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="8b11f-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="8b11f-140">Llama al <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensión y especifica `_myAllowSpecificOrigins` la Directiva CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="8b11f-141">`UseCors`agrega el middleware de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="8b11f-142">La llamada a `UseCors` debe colocarse después `UseRouting`de, pero `UseAuthorization`antes de.</span><span class="sxs-lookup"><span data-stu-id="8b11f-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="8b11f-143">Para obtener más información, vea [orden de middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="8b11f-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="8b11f-144">Llama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> a con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="8b11f-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="8b11f-145">La expresión lambda toma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> un objeto.</span><span class="sxs-lookup"><span data-stu-id="8b11f-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="8b11f-146">[Las opciones de configuración](#cors-policy-options), `WithOrigins`como, se describen más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="8b11f-147">Habilita la `_myAllowSpecificOrigins` Directiva CORS para todos los puntos de conexión del controlador.</span><span class="sxs-lookup"><span data-stu-id="8b11f-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="8b11f-148">Consulte [enrutamiento de puntos de conexión](#ecors) para aplicar una directiva de CORS a puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="8b11f-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="8b11f-149">Con el enrutamiento de punto de conexión, el middleware de CORS ***debe*** estar configurado para `UseRouting` ejecutarse entre las llamadas a y `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="8b11f-150">Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="8b11f-151">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega los servicios CORS al contenedor de servicio de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8b11f-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="8b11f-152">Para obtener más información, vea [Opciones de directiva de CORS](#cpo) en este documento.</span><span class="sxs-lookup"><span data-stu-id="8b11f-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="8b11f-153">Los <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos se pueden encadenar, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b11f-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="8b11f-154">Nota: la dirección URL especificada **no** debe contener una barra diagonal final`/`().</span><span class="sxs-lookup"><span data-stu-id="8b11f-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="8b11f-155">Si la dirección URL termina con `/`, la comparación devuelve `false` y no se devuelve ningún encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="8b11f-156">CORS con Directiva y middleware predeterminados</span><span class="sxs-lookup"><span data-stu-id="8b11f-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="8b11f-157">El siguiente código resaltado habilita la directiva predeterminada CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="8b11f-158">El código anterior aplica la directiva predeterminada CORS a todos los puntos de conexión del controlador.</span><span class="sxs-lookup"><span data-stu-id="8b11f-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="8b11f-159">Habilitación de CORS con enrutamiento de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="8b11f-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="8b11f-160">La habilitación de CORS en cada punto de `RequireCors` conexión mediante actualmente ***no*** admite [solicitudes preparatorias automáticas](#apf).</span><span class="sxs-lookup"><span data-stu-id="8b11f-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="8b11f-161">Para obtener más información, consulte [este problema de github](https://github.com/dotnet/aspnetcore/issues/20709) y [pruebe CORS con enrutamiento de puntos de conexión y [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="8b11f-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="8b11f-162">Con el enrutamiento de punto de conexión, CORS se puede habilitar en cada punto de <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> conexión mediante el conjunto de métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="8b11f-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="8b11f-163">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="8b11f-163">In the preceding code:</span></span>

* <span data-ttu-id="8b11f-164">`app.UseCors`habilita el middleware de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="8b11f-165">Dado que no se ha configurado una directiva `app.UseCors()` predeterminada, solo no habilita CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="8b11f-166">Los `/echo` puntos de conexión del controlador y permiten solicitudes entre orígenes mediante la Directiva especificada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="8b11f-167">Los `/echo2` extremos Razor de las páginas y ***no*** permiten solicitudes entre orígenes porque no se especificó ninguna directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="8b11f-168">El atributo [[DisableCors]](#dc) ***no*** deshabilita CORS habilitado por el enrutamiento de puntos de conexión `RequireCors`con.</span><span class="sxs-lookup"><span data-stu-id="8b11f-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="8b11f-169">Consulte [prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar código similar al anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="8b11f-170">Habilitación de CORS con atributos</span><span class="sxs-lookup"><span data-stu-id="8b11f-170">Enable CORS with attributes</span></span>

<span data-ttu-id="8b11f-171">La habilitación de CORS con el atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) y la aplicación de una directiva con nombre solo a los puntos de conexión que requieren CORS proporciona el control más fino.</span><span class="sxs-lookup"><span data-stu-id="8b11f-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="8b11f-172">El atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="8b11f-173">El `[EnableCors]` atributo habilita CORS para los puntos de conexión seleccionados, en lugar de todos los extremos:</span><span class="sxs-lookup"><span data-stu-id="8b11f-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="8b11f-174">`[EnableCors]`Especifica la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="8b11f-175">`[EnableCors("{Policy String}")]`especifica una directiva con nombre.</span><span class="sxs-lookup"><span data-stu-id="8b11f-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="8b11f-176">El `[EnableCors]` atributo se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="8b11f-177">Del`PageModel`</span><span class="sxs-lookup"><span data-stu-id="8b11f-177"> Page `PageModel`</span></span>
* <span data-ttu-id="8b11f-178">Controller</span><span class="sxs-lookup"><span data-stu-id="8b11f-178">Controller</span></span>
* <span data-ttu-id="8b11f-179">Método de acción del controlador</span><span class="sxs-lookup"><span data-stu-id="8b11f-179">Controller action method</span></span>

<span data-ttu-id="8b11f-180">Se pueden aplicar directivas diferentes a los controladores, modelos de página o métodos de acción `[EnableCors]` con el atributo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="8b11f-181">Cuando el `[EnableCors]` atributo se aplica a un controlador, un modelo de página o un método de acción y CORS está habilitado en middleware, se aplican ***ambas*** directivas.</span><span class="sxs-lookup"><span data-stu-id="8b11f-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="8b11f-182">***Se recomienda no combinar directivas. Use el*** `[EnableCors]` ***atributo o middleware, no ambos en la misma aplicación.***</span><span class="sxs-lookup"><span data-stu-id="8b11f-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="8b11f-183">En el código siguiente se aplica una directiva diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="8b11f-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="8b11f-184">En el código siguiente se crean dos directivas de CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="8b11f-185">Para el control más fino de la limitación de solicitudes de CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="8b11f-186">Se `[EnableCors("MyPolicy")]` usa con una directiva con nombre.</span><span class="sxs-lookup"><span data-stu-id="8b11f-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="8b11f-187">No defina una directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-187">Don't define a default policy.</span></span>
* <span data-ttu-id="8b11f-188">No use el [enrutamiento de puntos de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="8b11f-189">El código de la sección siguiente se ajusta a la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="8b11f-190">Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="8b11f-191">Deshabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-191">Disable CORS</span></span>

<span data-ttu-id="8b11f-192">El atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***no*** deshabilita CORS habilitado por el enrutamiento de [puntos de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="8b11f-193">En el código siguiente se define la `"MyPolicy"`Directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="8b11f-194">El código siguiente deshabilita CORS para la `GetValues2` acción:</span><span class="sxs-lookup"><span data-stu-id="8b11f-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="8b11f-195">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="8b11f-195">The preceding code:</span></span>

* <span data-ttu-id="8b11f-196">No habilita CORS con [enrutamiento de punto de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="8b11f-197">No define una [directiva predeterminada de CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="8b11f-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="8b11f-198">Usa [[EnableCors ("MyPolicy")]](#attr) para habilitar la `"MyPolicy"` Directiva CORS para el controlador.</span><span class="sxs-lookup"><span data-stu-id="8b11f-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="8b11f-199">Deshabilita CORS para el `GetValues2` método.</span><span class="sxs-lookup"><span data-stu-id="8b11f-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="8b11f-200">Consulte [prueba de CORS](#testc) para obtener instrucciones sobre cómo probar el código anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="8b11f-201">Opciones de directiva de CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-201">CORS policy options</span></span>

<span data-ttu-id="8b11f-202">En esta sección se describen las distintas opciones que se pueden establecer en una directiva de CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="8b11f-203">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="8b11f-204">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="8b11f-205">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="8b11f-206">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="8b11f-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="8b11f-207">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="8b11f-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="8b11f-208">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="8b11f-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="8b11f-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama a `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="8b11f-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b11f-210">Para algunas opciones, puede resultar útil leer la sección [Cómo funciona CORS](#how-cors) en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="8b11f-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="8b11f-211">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-211">Set the allowed origins</span></span>

<span data-ttu-id="8b11f-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitudes de CORS de todos los orígenes con cualquier`http` esquema `https`(o).</span><span class="sxs-lookup"><span data-stu-id="8b11f-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="8b11f-213">`AllowAnyOrigin`no es seguro porque *cualquier sitio web* puede realizar solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="8b11f-214">Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración no segura y puede dar lugar a la falsificación de solicitudes entre sitios.</span><span class="sxs-lookup"><span data-stu-id="8b11f-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="8b11f-215">El servicio CORS devuelve una respuesta de CORS no válida cuando se configura una aplicación con ambos métodos.</span><span class="sxs-lookup"><span data-stu-id="8b11f-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="8b11f-216">`AllowAnyOrigin`afecta a las solicitudes preparatorias `Access-Control-Allow-Origin` y al encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="8b11f-217">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="8b11f-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Establece la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propiedad de la Directiva para que sea una función que permita que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="8b11f-219">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="8b11f-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="8b11f-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="8b11f-221">Permite cualquier método HTTP:</span><span class="sxs-lookup"><span data-stu-id="8b11f-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="8b11f-222">Afecta a las solicitudes preparatorias `Access-Control-Allow-Methods` y al encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="8b11f-223">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="8b11f-224">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-224">Set the allowed request headers</span></span>

<span data-ttu-id="8b11f-225">Para permitir el envío de encabezados específicos en una solicitud de CORS, denominados [encabezados de solicitud](https://xhr.spec.whatwg.org/#request)de autor <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> , llame a y especifique los encabezados permitidos:</span><span class="sxs-lookup"><span data-stu-id="8b11f-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="8b11f-226">Para permitir todos los [encabezados de solicitud](https://www.w3.org/TR/cors/#author-request-headers)de autor <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, llame a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="8b11f-227">`AllowAnyHeader`afecta a las solicitudes preparatorias y al encabezado [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="8b11f-228">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="8b11f-229">Una coincidencia de directiva de middleware de CORS con encabezados específicos `WithHeaders` especificados por solo es posible cuando los encabezados `Access-Control-Request-Headers` enviados coinciden exactamente con los encabezados `WithHeaders`indicados en.</span><span class="sxs-lookup"><span data-stu-id="8b11f-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="8b11f-230">Por ejemplo, considere una aplicación configurada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="8b11f-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="8b11f-231">Middleware de CORS rechaza una solicitud preparatoria con el siguiente encabezado de solicitud porque `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) no aparece en `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="8b11f-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="8b11f-232">La aplicación devuelve una respuesta *200 OK* pero no envía los encabezados de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="8b11f-233">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="8b11f-234">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="8b11f-234">Set the exposed response headers</span></span>

<span data-ttu-id="8b11f-235">De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="8b11f-236">Para obtener más información, consulte [uso compartido de recursos entre orígenes (terminología) de W3C: encabezado de respuesta simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="8b11f-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="8b11f-237">Los encabezados de respuesta que están disponibles de forma predeterminada son:</span><span class="sxs-lookup"><span data-stu-id="8b11f-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="8b11f-238">La especificación CORS llama a estos encabezados de *respuesta simple*.</span><span class="sxs-lookup"><span data-stu-id="8b11f-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="8b11f-239">Para que otros encabezados estén disponibles para la aplicación, llame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="8b11f-240">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="8b11f-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="8b11f-241">Las credenciales requieren un tratamiento especial en una solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="8b11f-242">De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="8b11f-243">Las credenciales incluyen cookies y esquemas de autenticación HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b11f-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="8b11f-244">Para enviar credenciales con una solicitud entre orígenes, el cliente debe establecer `XMLHttpRequest.withCredentials` en `true`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="8b11f-245">Usar `XMLHttpRequest` directamente:</span><span class="sxs-lookup"><span data-stu-id="8b11f-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="8b11f-246">Usar jQuery:</span><span class="sxs-lookup"><span data-stu-id="8b11f-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="8b11f-247">Uso de la [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="8b11f-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="8b11f-248">El servidor debe permitir las credenciales.</span><span class="sxs-lookup"><span data-stu-id="8b11f-248">The server must allow the credentials.</span></span> <span data-ttu-id="8b11f-249">Para permitir las credenciales entre orígenes, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>llame a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="8b11f-250">La respuesta HTTP incluye un `Access-Control-Allow-Credentials` encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="8b11f-251">Si el explorador envía credenciales pero la respuesta no incluye un `Access-Control-Allow-Credentials` encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="8b11f-252">Permitir las credenciales entre orígenes es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="8b11f-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="8b11f-253">Un sitio web en otro dominio puede enviar las credenciales del usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="8b11f-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="8b11f-254">La especificación CORS también indica que el establecimiento de `"*"` orígenes en (todos los orígenes) no `Access-Control-Allow-Credentials` es válido si el encabezado está presente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="8b11f-255">Solicitudes preparatorias</span><span class="sxs-lookup"><span data-stu-id="8b11f-255">Preflight requests</span></span>

<span data-ttu-id="8b11f-256">En algunas solicitudes de CORS, el explorador envía una solicitud [Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de efectuar la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="8b11f-257">Esta solicitud se denomina [solicitud preparatoria](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="8b11f-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="8b11f-258">El explorador puede omitir la solicitud preparatoria si se cumplen ***todas*** las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="8b11f-259">El método de solicitud es GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="8b11f-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="8b11f-260">La aplicación no establece encabezados de solicitud distintos de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`o `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="8b11f-261">El `Content-Type` encabezado, si está establecido, tiene uno de los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="8b11f-262">La regla de los encabezados de solicitud establecidos para la solicitud de cliente se aplica a los encabezados que establece la `setRequestHeader` aplicación mediante `XMLHttpRequest` una llamada a en el objeto.</span><span class="sxs-lookup"><span data-stu-id="8b11f-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="8b11f-263">La especificación CORS llama a estos encabezados de [solicitud Author](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="8b11f-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="8b11f-264">La regla no se aplica a los encabezados que el explorador puede establecer, `User-Agent`como `Host`, o `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="8b11f-265">A continuación se incluye una respuesta de ejemplo similar a la solicitud preparatoria realizada desde el botón **[Put Test]** de la sección [Test CORS](#testc) de este documento.</span><span class="sxs-lookup"><span data-stu-id="8b11f-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="8b11f-266">La solicitud preparatoria usa el método [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="8b11f-267">Puede incluir los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-267">It may include the following headers:</span></span>

* <span data-ttu-id="8b11f-268">[Access-Control-request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): método HTTP que se utilizará para la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="8b11f-269">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): una lista de encabezados de solicitud que la aplicación establece en la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="8b11f-270">Como se indicó anteriormente, esto no incluye los encabezados que establece el explorador, como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="8b11f-271">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="8b11f-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="8b11f-272">Si se deniega la solicitud preparatoria, la aplicación devuelve una `200 OK` respuesta pero no establece los encabezados CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="8b11f-273">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="8b11f-274">Para obtener un ejemplo de una solicitud preparatoria denegada, consulte la sección [prueba CORS](#testc) de este documento.</span><span class="sxs-lookup"><span data-stu-id="8b11f-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="8b11f-275">Con las herramientas F12, la aplicación de consola muestra un error similar a uno de los siguientes, en función del explorador:</span><span class="sxs-lookup"><span data-stu-id="8b11f-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="8b11f-276">Firefox: solicitud entre orígenes bloqueada: la misma directiva de origen no permite la lectura del recurso remoto `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`en.</span><span class="sxs-lookup"><span data-stu-id="8b11f-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="8b11f-277">(Motivo: la solicitud de CORS no se realizó correctamente).</span><span class="sxs-lookup"><span data-stu-id="8b11f-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="8b11f-278">Más información</span><span class="sxs-lookup"><span data-stu-id="8b11f-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="8b11f-279">Basado en cromo: el acceso a lahttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5captura en ' 'https://cors3.azurewebsites.netdesde el origen ' ' se ha bloqueado mediante la Directiva de CORS: la respuesta a la solicitud preparatoria no pasa la comprobación de control de acceso: no hay ningún encabezado ' Access-Control-Allow-Origin ' presente en el recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="8b11f-280">Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="8b11f-281">Para permitir encabezados específicos, llame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="8b11f-282">Para permitir todos los [encabezados de solicitud](https://www.w3.org/TR/cors/#author-request-headers)de autor <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, llame a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="8b11f-283">Los exploradores no son coherentes en `Access-Control-Request-Headers`el modo en que se establecen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="8b11f-284">Si:</span><span class="sxs-lookup"><span data-stu-id="8b11f-284">If either:</span></span>

* <span data-ttu-id="8b11f-285">Los encabezados se establecen en un valor distinto de`"*"`</span><span class="sxs-lookup"><span data-stu-id="8b11f-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="8b11f-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se llama a: incluye al `Accept`menos `Content-Type`, y `Origin`, además de los encabezados personalizados que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="8b11f-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="8b11f-287">Código de solicitud de comprobación previa automática</span><span class="sxs-lookup"><span data-stu-id="8b11f-287">Automatic preflight request code</span></span>

<span data-ttu-id="8b11f-288">Cuando se aplica la Directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="8b11f-289">Globalmente llamando a `app.UseCors` en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="8b11f-290">Usar el `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="8b11f-291">ASP.NET Core responde a la solicitud de opciones preparatorias.</span><span class="sxs-lookup"><span data-stu-id="8b11f-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="8b11f-292">La habilitación de CORS en cada punto de `RequireCors` conexión mediante actualmente ***no*** admite solicitudes preparatorias automáticas.</span><span class="sxs-lookup"><span data-stu-id="8b11f-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="8b11f-293">En la sección [prueba CORS](#testc) de este documento se muestra este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="8b11f-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="8b11f-294">Atributo [HttpOptions] para solicitudes preparatorias</span><span class="sxs-lookup"><span data-stu-id="8b11f-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="8b11f-295">Cuando CORS está habilitado con la Directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes preparatorias de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="8b11f-296">En algunos escenarios, puede que este no sea el caso.</span><span class="sxs-lookup"><span data-stu-id="8b11f-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="8b11f-297">Por ejemplo, con [CORS con enrutamiento de punto de conexión](#ecors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="8b11f-298">En el código siguiente se usa el atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para crear extremos para las solicitudes Options:</span><span class="sxs-lookup"><span data-stu-id="8b11f-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="8b11f-299">Consulte [prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar el código anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="8b11f-300">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="8b11f-300">Set the preflight expiration time</span></span>

<span data-ttu-id="8b11f-301">El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud preparatoria.</span><span class="sxs-lookup"><span data-stu-id="8b11f-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="8b11f-302">Para establecer este encabezado, llame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="8b11f-303">Cómo funciona CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-303">How CORS works</span></span>

<span data-ttu-id="8b11f-304">En esta sección se describe lo que sucede en una solicitud de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes http.</span><span class="sxs-lookup"><span data-stu-id="8b11f-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="8b11f-305">CORS **no** es una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8b11f-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="8b11f-306">CORS es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="8b11f-307">Por ejemplo, un actor malintencionado podría usar el [scripting entre sitios (XSS)](xref:security/cross-site-scripting) en el sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.</span><span class="sxs-lookup"><span data-stu-id="8b11f-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="8b11f-308">Una API no es más segura, ya que permite CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="8b11f-309">Es el cliente (explorador) el que debe aplicar CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="8b11f-310">El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="8b11f-311">Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:</span><span class="sxs-lookup"><span data-stu-id="8b11f-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="8b11f-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="8b11f-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="8b11f-313">Postman</span><span class="sxs-lookup"><span data-stu-id="8b11f-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="8b11f-314">HttpClient de .NET</span><span class="sxs-lookup"><span data-stu-id="8b11f-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="8b11f-315">Un explorador web escribiendo la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="8b11f-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="8b11f-316">Es una forma de que un servidor permita que los exploradores ejecuten una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) entre orígenes que, de lo contrario, se prohibirá.</span><span class="sxs-lookup"><span data-stu-id="8b11f-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="8b11f-317">Los exploradores sin CORS no pueden realizar solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="8b11f-318">Antes de CORS, se usaba [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) para eludir esta restricción.</span><span class="sxs-lookup"><span data-stu-id="8b11f-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="8b11f-319">JSONP no usa XHR, usa la `<script>` etiqueta para recibir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="8b11f-320">Los scripts pueden cargarse entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="8b11f-321">La [especificación CORS](https://www.w3.org/TR/cors/) presentó varios encabezados HTTP nuevos que permiten solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="8b11f-322">Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="8b11f-323">No es necesario el código personalizado de JavaScript para habilitar CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="8b11f-324">[Botón poner prueba](https://cors3.azurewebsites.net/test) en el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implementado</span><span class="sxs-lookup"><span data-stu-id="8b11f-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="8b11f-325">A continuación se presenta un ejemplo de una solicitud entre orígenes desde el [Values](https://cors3.azurewebsites.net/) botón de prueba valores `https://cors1.azurewebsites.net/api/values`a.</span><span class="sxs-lookup"><span data-stu-id="8b11f-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="8b11f-326">El `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="8b11f-326">The `Origin` header:</span></span>

* <span data-ttu-id="8b11f-327">Proporciona el dominio del sitio que realiza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="8b11f-328">Es obligatorio y debe ser diferente del host.</span><span class="sxs-lookup"><span data-stu-id="8b11f-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="8b11f-329">**Encabezados generales**</span><span class="sxs-lookup"><span data-stu-id="8b11f-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="8b11f-330">**Encabezados de respuesta**</span><span class="sxs-lookup"><span data-stu-id="8b11f-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="8b11f-331">**Encabezados de solicitud**</span><span class="sxs-lookup"><span data-stu-id="8b11f-331">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="8b11f-332">En `OPTIONS` las solicitudes, el servidor establece **Response headers** `Access-Control-Allow-Origin: {allowed origin}` el encabezado de encabezados de respuesta en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="8b11f-333">Por ejemplo, la solicitud de `OPTIONS` botón [eliminar [EnableCors]](https://cors1.azurewebsites.net/test?number=2) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado contiene los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="8b11f-334">**Encabezados generales**</span><span class="sxs-lookup"><span data-stu-id="8b11f-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="8b11f-335">**Encabezados de respuesta**</span><span class="sxs-lookup"><span data-stu-id="8b11f-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="8b11f-336">**Encabezados de solicitud**</span><span class="sxs-lookup"><span data-stu-id="8b11f-336">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="8b11f-337">En los **encabezados de respuesta**anteriores, el servidor establece el encabezado [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="8b11f-338">El `https://cors1.azurewebsites.net` valor de este encabezado coincide con `Origin` el encabezado de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="8b11f-339">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> se llama a, `Access-Control-Allow-Origin: *`se devuelve el valor del carácter comodín.</span><span class="sxs-lookup"><span data-stu-id="8b11f-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="8b11f-340">`AllowAnyOrigin`permite cualquier origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="8b11f-341">Si la respuesta no incluye el `Access-Control-Allow-Origin` encabezado, se produce un error en la solicitud de origen cruzado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="8b11f-342">En concreto, el explorador no permite la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="8b11f-343">Aunque el servidor devuelva una respuesta correcta, el explorador no pone la respuesta a disposición de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="8b11f-344">Mostrar solicitudes de opciones</span><span class="sxs-lookup"><span data-stu-id="8b11f-344">Display OPTIONS requests</span></span>

<span data-ttu-id="8b11f-345">De forma predeterminada, los exploradores Chrome y Edge no muestran las solicitudes de opciones en la pestaña red de las herramientas F12.</span><span class="sxs-lookup"><span data-stu-id="8b11f-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="8b11f-346">Para mostrar las solicitudes OPTIONs en estos exploradores:</span><span class="sxs-lookup"><span data-stu-id="8b11f-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="8b11f-347">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="8b11f-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="8b11f-348">deshabilite la marca.</span><span class="sxs-lookup"><span data-stu-id="8b11f-348">disable the flag.</span></span>
* <span data-ttu-id="8b11f-349">Reiniciar.</span><span class="sxs-lookup"><span data-stu-id="8b11f-349">restart.</span></span>

<span data-ttu-id="8b11f-350">Firefox muestra las solicitudes de opciones de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="8b11f-351">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="8b11f-351">CORS in IIS</span></span>

<span data-ttu-id="8b11f-352">Al implementar en IIS, CORS debe ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="8b11f-353">Para admitir este escenario, es necesario instalar y configurar el [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="8b11f-354">Prueba de CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-354">Test CORS</span></span>

<span data-ttu-id="8b11f-355">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tiene código para probar CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="8b11f-356">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="8b11f-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="8b11f-357">El ejemplo es un proyecto de API Razor con páginas agregadas:</span><span class="sxs-lookup"><span data-stu-id="8b11f-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="8b11f-358">`WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar a la [descarga del código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="8b11f-359">A continuación `ValuesController` se proporcionan los puntos de conexión para las pruebas:</span><span class="sxs-lookup"><span data-stu-id="8b11f-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="8b11f-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) lo proporciona el paquete de NuGet [Rick. docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) y muestra información de ruta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="8b11f-361">Pruebe el código de ejemplo anterior utilizando uno de los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="8b11f-362">Use la aplicación de ejemplo implementada en [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="8b11f-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="8b11f-363">No es necesario descargar el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="8b11f-364">Ejecute el ejemplo con `dotnet run` el uso de la dirección `https://localhost:5001`URL predeterminada de.</span><span class="sxs-lookup"><span data-stu-id="8b11f-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="8b11f-365">Ejecute el ejemplo desde Visual Studio con el puerto establecido en 44398 para una dirección URL `https://localhost:44398`de.</span><span class="sxs-lookup"><span data-stu-id="8b11f-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="8b11f-366">Usar un explorador con las herramientas F12:</span><span class="sxs-lookup"><span data-stu-id="8b11f-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="8b11f-367">Seleccione el botón **valores** y revise los encabezados en la pestaña **red** .</span><span class="sxs-lookup"><span data-stu-id="8b11f-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="8b11f-368">Seleccione el botón **poner prueba** .</span><span class="sxs-lookup"><span data-stu-id="8b11f-368">Select the **PUT test** button.</span></span> <span data-ttu-id="8b11f-369">Consulte [solicitudes de visualización de opciones](#options) para obtener instrucciones sobre cómo mostrar la solicitud Options.</span><span class="sxs-lookup"><span data-stu-id="8b11f-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="8b11f-370">La **prueba Put** crea dos solicitudes, una solicitud preparatoria de opciones y la solicitud Put.</span><span class="sxs-lookup"><span data-stu-id="8b11f-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="8b11f-371">Seleccione el **`GetValues2 [DisableCors]`** botón para desencadenar una solicitud de CORS con errores.</span><span class="sxs-lookup"><span data-stu-id="8b11f-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="8b11f-372">Como se mencionó en el documento, la respuesta devuelve 200 Success, pero no se realiza la solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="8b11f-373">Seleccione la pestaña **consola** para ver el error de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="8b11f-374">Dependiendo del explorador, se muestra un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b11f-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="8b11f-375">La directiva `'https://cors1.azurewebsites.net/api/values/GetValues2'` de CORS bloqueó `'https://cors3.azurewebsites.net'` el acceso a la captura desde el origen: no hay ningún encabezado "Access-Control-Allow-Origin" en el recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="8b11f-376">Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="8b11f-377">Los puntos de conexión habilitados para CORS se pueden probar con una herramienta, como [rizo](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="8b11f-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="8b11f-378">Cuando se usa una herramienta, el origen de la solicitud especificada por `Origin` el encabezado debe ser diferente del host que recibe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="8b11f-379">Si la solicitud no es de *origen cruzado* según el valor del `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="8b11f-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="8b11f-380">No es necesario que el middleware de CORS procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="8b11f-381">Los encabezados CORS no se devuelven en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="8b11f-382">El siguiente comando usa `curl` para emitir una solicitud Options con información:</span><span class="sxs-lookup"><span data-stu-id="8b11f-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="8b11f-383">Prueba de CORS con enrutamiento de punto de conexión y [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="8b11f-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="8b11f-384">La habilitación de CORS en cada punto de `RequireCors` conexión mediante actualmente ***no*** admite [solicitudes preparatorias automáticas](#apf).</span><span class="sxs-lookup"><span data-stu-id="8b11f-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="8b11f-385">Considere el código siguiente, que usa el [enrutamiento de punto de conexión para habilitar CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="8b11f-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="8b11f-386">A continuación `TodoItems1Controller` se proporcionan los puntos de conexión para las pruebas:</span><span class="sxs-lookup"><span data-stu-id="8b11f-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="8b11f-387">Pruebe el código anterior de la [Página de prueba](https://cors1.azurewebsites.net/test?number=1) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="8b11f-388">Los botones **Delete [EnableCors]** y **Get [EnableCors]** se ejecutan correctamente, ya que `[EnableCors]` los extremos tienen y responden a las solicitudes preparatorias.</span><span class="sxs-lookup"><span data-stu-id="8b11f-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="8b11f-389">Se produce un error en los otros puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="8b11f-389">The other endpoints fails.</span></span> <span data-ttu-id="8b11f-390">Se produce un error en el botón **Get** porque [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envía:</span><span class="sxs-lookup"><span data-stu-id="8b11f-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="8b11f-391">Lo siguiente `TodoItems2Controller` proporciona puntos de conexión similares, pero incluye código explícito para responder a las solicitudes de opciones:</span><span class="sxs-lookup"><span data-stu-id="8b11f-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="8b11f-392">Pruebe el código anterior de la [Página de prueba](https://cors1.azurewebsites.net/test?number=2) del ejemplo implementado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="8b11f-393">En la lista desplegable **controlador** , seleccione **preparatoria** y después **establezca controlador**.</span><span class="sxs-lookup"><span data-stu-id="8b11f-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="8b11f-394">Todas las llamadas a CORS a `TodoItems2Controller` los puntos de conexión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b11f-395">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8b11f-395">Additional resources</span></span>

* [<span data-ttu-id="8b11f-396">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="8b11f-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="8b11f-397">Introducción al módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b11f-398">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b11f-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8b11f-399">En este artículo se muestra cómo habilitar CORS en una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b11f-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="8b11f-400">La seguridad del explorador evita que una página web realice solicitudes a un dominio diferente del que atendió a dicha página web.</span><span class="sxs-lookup"><span data-stu-id="8b11f-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8b11f-401">Esta restricción se denomina *directiva de mismo origen*.</span><span class="sxs-lookup"><span data-stu-id="8b11f-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="8b11f-402">La directiva de mismo origen evita que un sitio malintencionado lea información confidencial de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="8b11f-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8b11f-403">En ocasiones, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="8b11f-404">Para obtener más información, consulte el [artículo Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="8b11f-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="8b11f-405">[Uso compartido de recursos entre orígenes](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="8b11f-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="8b11f-406">Es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="8b11f-407">**No** es una característica de seguridad, CORS reduce la seguridad.</span><span class="sxs-lookup"><span data-stu-id="8b11f-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="8b11f-408">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="8b11f-409">Para obtener más información, vea [Cómo funciona CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="8b11f-410">Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras se rechazan otras.</span><span class="sxs-lookup"><span data-stu-id="8b11f-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="8b11f-411">Es más seguro y más flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="8b11f-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="8b11f-412">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b11f-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="8b11f-413">Mismo origen</span><span class="sxs-lookup"><span data-stu-id="8b11f-413">Same origin</span></span>

<span data-ttu-id="8b11f-414">Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="8b11f-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="8b11f-415">Estas dos direcciones URL tienen el mismo origen:</span><span class="sxs-lookup"><span data-stu-id="8b11f-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="8b11f-416">Estas direcciones URL tienen distintos orígenes que las dos direcciones URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="8b11f-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="8b11f-417">`https://example.net`&ndash; Dominio diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="8b11f-418">`https://www.example.com/foo.html`&ndash; Subdominio diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="8b11f-419">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="8b11f-420">`https://example.com:9000/foo.html`&ndash; Puerto diferente</span><span class="sxs-lookup"><span data-stu-id="8b11f-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="8b11f-421">Internet Explorer no tiene en cuenta el puerto al comparar los orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="8b11f-422">CORS con Directiva con nombre y middleware</span><span class="sxs-lookup"><span data-stu-id="8b11f-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="8b11f-423">Middleware de CORS controla las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="8b11f-424">El código siguiente habilita CORS para toda la aplicación con el origen especificado:</span><span class="sxs-lookup"><span data-stu-id="8b11f-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="8b11f-425">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="8b11f-425">The preceding code:</span></span>

* <span data-ttu-id="8b11f-426">Establece el nombre de la directiva\_en "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="8b11f-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="8b11f-427">El nombre de la Directiva es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="8b11f-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="8b11f-428">Llama al <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensión, que habilita CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="8b11f-429">Llama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> a con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="8b11f-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="8b11f-430">La expresión lambda toma <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> un objeto.</span><span class="sxs-lookup"><span data-stu-id="8b11f-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="8b11f-431">[Las opciones de configuración](#cors-policy-options), `WithOrigins`como, se describen más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="8b11f-432">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega los servicios CORS al contenedor de servicio de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8b11f-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="8b11f-433">Para obtener más información, vea [Opciones de directiva de CORS](#cpo) en este documento.</span><span class="sxs-lookup"><span data-stu-id="8b11f-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="8b11f-434">El <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método puede encadenar métodos, tal y como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b11f-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="8b11f-435">Nota: la dirección URL **no** debe contener una barra diagonal final`/`().</span><span class="sxs-lookup"><span data-stu-id="8b11f-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="8b11f-436">Si la dirección URL termina con `/`, la comparación devuelve `false` y no se devuelve ningún encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="8b11f-437">El código siguiente aplica las directivas de CORS a todos los puntos de conexión de aplicaciones a través del middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="8b11f-438">Nota: `UseCors` se debe llamar a `UseMvc`antes de.</span><span class="sxs-lookup"><span data-stu-id="8b11f-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="8b11f-439">Consulte [habilitación de Razor CORS en páginas, controladores y métodos de acción](#ecors) para aplicar la Directiva CORS en el nivel de página/controlador/acción.</span><span class="sxs-lookup"><span data-stu-id="8b11f-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="8b11f-440">Consulte [prueba de CORS](#test) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="8b11f-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="8b11f-441">Habilitación de CORS con atributos</span><span class="sxs-lookup"><span data-stu-id="8b11f-441">Enable CORS with attributes</span></span>

<span data-ttu-id="8b11f-442">El [ &lbrack;atributo&rbrack; EnableCors](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="8b11f-443">El `[EnableCors]` atributo habilita CORS para los puntos de conexión seleccionados, en lugar de todos los extremos.</span><span class="sxs-lookup"><span data-stu-id="8b11f-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="8b11f-444">Se `[EnableCors]` usa para especificar la directiva predeterminada `[EnableCors("{Policy String}")]` y para especificar una directiva.</span><span class="sxs-lookup"><span data-stu-id="8b11f-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="8b11f-445">El `[EnableCors]` atributo se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="8b11f-446">Del`PageModel`</span><span class="sxs-lookup"><span data-stu-id="8b11f-446"> Page `PageModel`</span></span>
* <span data-ttu-id="8b11f-447">Controller</span><span class="sxs-lookup"><span data-stu-id="8b11f-447">Controller</span></span>
* <span data-ttu-id="8b11f-448">Método de acción del controlador</span><span class="sxs-lookup"><span data-stu-id="8b11f-448">Controller action method</span></span>

<span data-ttu-id="8b11f-449">Puede aplicar diferentes directivas al controlador/página-modelo o acción con el `[EnableCors]` atributo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="8b11f-450">Cuando el `[EnableCors]` atributo se aplica a un método de acción/modelo de página/controladores y CORS está habilitado en middleware, se aplican ***ambas*** directivas.</span><span class="sxs-lookup"><span data-stu-id="8b11f-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="8b11f-451">Se recomienda ***no*** combinar directivas.</span><span class="sxs-lookup"><span data-stu-id="8b11f-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="8b11f-452">Use el `[EnableCors]` atributo o middleware, \***no ambos**.</span><span class="sxs-lookup"><span data-stu-id="8b11f-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="8b11f-453">Al usar `[EnableCors]`, **no** defina una directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="8b11f-454">En el código siguiente se aplica una directiva diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="8b11f-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="8b11f-455">En el código siguiente se crea una directiva predeterminada de CORS y `"AnotherPolicy"`una directiva denominada:</span><span class="sxs-lookup"><span data-stu-id="8b11f-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="8b11f-456">Deshabilitar CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-456">Disable CORS</span></span>

<span data-ttu-id="8b11f-457">El [ &lbrack;atributo&rbrack; DisableCors](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deshabilita CORS para el controlador/página-modelo/acción.</span><span class="sxs-lookup"><span data-stu-id="8b11f-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="8b11f-458">Opciones de directiva de CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-458">CORS policy options</span></span>

<span data-ttu-id="8b11f-459">En esta sección se describen las distintas opciones que se pueden establecer en una directiva de CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="8b11f-460">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="8b11f-461">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="8b11f-462">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="8b11f-463">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="8b11f-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="8b11f-464">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="8b11f-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="8b11f-465">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="8b11f-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="8b11f-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama a `Startup.ConfigureServices`en.</span><span class="sxs-lookup"><span data-stu-id="8b11f-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b11f-467">Para algunas opciones, puede resultar útil leer la sección [Cómo funciona CORS](#how-cors) en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="8b11f-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="8b11f-468">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-468">Set the allowed origins</span></span>

<span data-ttu-id="8b11f-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitudes de CORS de todos los orígenes con cualquier`http` esquema `https`(o).</span><span class="sxs-lookup"><span data-stu-id="8b11f-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="8b11f-470">`AllowAnyOrigin`no es seguro porque *cualquier sitio web* puede realizar solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="8b11f-471">Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración no segura y puede dar lugar a la falsificación de solicitudes entre sitios.</span><span class="sxs-lookup"><span data-stu-id="8b11f-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="8b11f-472">En el caso de una aplicación segura, especifique una lista exacta de orígenes si el cliente debe autorizarse para obtener acceso a los recursos del servidor.</span><span class="sxs-lookup"><span data-stu-id="8b11f-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="8b11f-473">`AllowAnyOrigin`afecta a las solicitudes preparatorias `Access-Control-Allow-Origin` y al encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="8b11f-474">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="8b11f-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Establece la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propiedad de la Directiva para que sea una función que permita que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="8b11f-476">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="8b11f-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="8b11f-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="8b11f-478">Permite cualquier método HTTP:</span><span class="sxs-lookup"><span data-stu-id="8b11f-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="8b11f-479">Afecta a las solicitudes preparatorias `Access-Control-Allow-Methods` y al encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="8b11f-480">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="8b11f-481">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="8b11f-481">Set the allowed request headers</span></span>

<span data-ttu-id="8b11f-482">Para permitir el envío de encabezados específicos en una solicitud de CORS, denominados *encabezados de solicitud*de autor <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> , llame a y especifique los encabezados permitidos:</span><span class="sxs-lookup"><span data-stu-id="8b11f-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="8b11f-483">Para permitir todos los encabezados de solicitud de autor <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, llame a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="8b11f-484">Esta configuración afecta a las solicitudes preparatorias `Access-Control-Request-Headers` y al encabezado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="8b11f-485">Para obtener más información, consulte la sección [solicitudes preparatorias](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="8b11f-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="8b11f-486">El middleware de CORS siempre permite que se envíen cuatro encabezados en el `Access-Control-Request-Headers` , independientemente de los valores configurados en CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="8b11f-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="8b11f-487">Esta lista de encabezados incluye:</span><span class="sxs-lookup"><span data-stu-id="8b11f-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="8b11f-488">Por ejemplo, considere una aplicación configurada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="8b11f-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="8b11f-489">El middleware de CORS responde correctamente a una solicitud preparatoria con el siguiente encabezado de solicitud `Content-Language` porque siempre está en la lista de permitidos:</span><span class="sxs-lookup"><span data-stu-id="8b11f-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="8b11f-490">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="8b11f-490">Set the exposed response headers</span></span>

<span data-ttu-id="8b11f-491">De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="8b11f-492">Para obtener más información, consulte [uso compartido de recursos entre orígenes (terminología) de W3C: encabezado de respuesta simple](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="8b11f-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="8b11f-493">Los encabezados de respuesta que están disponibles de forma predeterminada son:</span><span class="sxs-lookup"><span data-stu-id="8b11f-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="8b11f-494">La especificación CORS llama a estos encabezados de *respuesta simple*.</span><span class="sxs-lookup"><span data-stu-id="8b11f-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="8b11f-495">Para que otros encabezados estén disponibles para la aplicación, llame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="8b11f-496">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="8b11f-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="8b11f-497">Las credenciales requieren un tratamiento especial en una solicitud de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="8b11f-498">De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="8b11f-499">Las credenciales incluyen cookies y esquemas de autenticación HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b11f-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="8b11f-500">Para enviar credenciales con una solicitud entre orígenes, el cliente debe establecer `XMLHttpRequest.withCredentials` en `true`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="8b11f-501">Usar `XMLHttpRequest` directamente:</span><span class="sxs-lookup"><span data-stu-id="8b11f-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="8b11f-502">Usar jQuery:</span><span class="sxs-lookup"><span data-stu-id="8b11f-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="8b11f-503">Uso de la [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="8b11f-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="8b11f-504">El servidor debe permitir las credenciales.</span><span class="sxs-lookup"><span data-stu-id="8b11f-504">The server must allow the credentials.</span></span> <span data-ttu-id="8b11f-505">Para permitir las credenciales entre orígenes, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>llame a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="8b11f-506">La respuesta HTTP incluye un `Access-Control-Allow-Credentials` encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="8b11f-507">Si el explorador envía credenciales pero la respuesta no incluye un `Access-Control-Allow-Credentials` encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="8b11f-508">Permitir las credenciales entre orígenes es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="8b11f-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="8b11f-509">Un sitio web en otro dominio puede enviar las credenciales del usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="8b11f-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="8b11f-510">La especificación CORS también indica que el establecimiento de `"*"` orígenes en (todos los orígenes) no `Access-Control-Allow-Credentials` es válido si el encabezado está presente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="8b11f-511">Solicitudes preparatorias</span><span class="sxs-lookup"><span data-stu-id="8b11f-511">Preflight requests</span></span>

<span data-ttu-id="8b11f-512">En algunas solicitudes de CORS, el explorador envía una solicitud adicional antes de efectuar la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="8b11f-513">Esta solicitud se denomina *solicitud preparatoria*.</span><span class="sxs-lookup"><span data-stu-id="8b11f-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="8b11f-514">El explorador puede omitir la solicitud preparatoria si se cumplen las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="8b11f-515">El método de solicitud es GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="8b11f-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="8b11f-516">La aplicación no establece encabezados de solicitud distintos de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`o `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="8b11f-517">El `Content-Type` encabezado, si está establecido, tiene uno de los valores siguientes:</span><span class="sxs-lookup"><span data-stu-id="8b11f-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="8b11f-518">La regla de los encabezados de solicitud establecidos para la solicitud de cliente se aplica a los encabezados que establece la `setRequestHeader` aplicación mediante `XMLHttpRequest` una llamada a en el objeto.</span><span class="sxs-lookup"><span data-stu-id="8b11f-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="8b11f-519">La especificación CORS llama a estos encabezados de *solicitud Author*.</span><span class="sxs-lookup"><span data-stu-id="8b11f-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="8b11f-520">La regla no se aplica a los encabezados que el explorador puede establecer, `User-Agent`como `Host`, o `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="8b11f-521">El siguiente es un ejemplo de una solicitud preparatoria:</span><span class="sxs-lookup"><span data-stu-id="8b11f-521">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="8b11f-522">La solicitud previa al vuelo usa el método HTTP OPTIONs.</span><span class="sxs-lookup"><span data-stu-id="8b11f-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="8b11f-523">Incluye dos encabezados especiales:</span><span class="sxs-lookup"><span data-stu-id="8b11f-523">It includes two special headers:</span></span>

* <span data-ttu-id="8b11f-524">`Access-Control-Request-Method`: Método HTTP que se usará para la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="8b11f-525">`Access-Control-Request-Headers`: Una lista de encabezados de solicitud que la aplicación establece en la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="8b11f-526">Como se indicó anteriormente, esto no incluye los encabezados que establece el explorador, como `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="8b11f-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="8b11f-527">Cuando CORS está habilitado con la Directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes preparatorias de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="8b11f-528">Vea el [atributo [HttpOptions] para solicitudes preparatorias](#pro).</span><span class="sxs-lookup"><span data-stu-id="8b11f-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="8b11f-529">Una solicitud preparatoria de CORS podría incluir un `Access-Control-Request-Headers` encabezado, que indica al servidor los encabezados que se envían con la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="8b11f-530">Para permitir encabezados específicos, llame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="8b11f-531">Para permitir todos los encabezados de solicitud de autor <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, llame a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="8b11f-532">Los exploradores no son totalmente coherentes en `Access-Control-Request-Headers`el modo en que se establecen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="8b11f-533">Si establece encabezados en un `"*"` valor distinto de (o use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), debe incluir al menos `Accept`, `Content-Type`y `Origin`, además de los encabezados personalizados que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="8b11f-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="8b11f-534">A continuación se ofrece un ejemplo de respuesta a la solicitud preparatoria (siempre que el servidor permita la solicitud):</span><span class="sxs-lookup"><span data-stu-id="8b11f-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="8b11f-535">La respuesta incluye un `Access-Control-Allow-Methods` encabezado que enumera los métodos permitidos y, opcionalmente, un `Access-Control-Allow-Headers` encabezado, que enumera los encabezados permitidos.</span><span class="sxs-lookup"><span data-stu-id="8b11f-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="8b11f-536">Si la solicitud preparatoria se realiza correctamente, el explorador envía la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="8b11f-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="8b11f-537">Si se deniega la solicitud preparatoria, la aplicación devuelve una respuesta *200 OK* pero no envía los encabezados de CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="8b11f-538">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="8b11f-539">Establecer la hora de expiración de la comprobación previa</span><span class="sxs-lookup"><span data-stu-id="8b11f-539">Set the preflight expiration time</span></span>

<span data-ttu-id="8b11f-540">El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud preparatoria.</span><span class="sxs-lookup"><span data-stu-id="8b11f-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="8b11f-541">Para establecer este encabezado, llame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>a:</span><span class="sxs-lookup"><span data-stu-id="8b11f-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="8b11f-542">Cómo funciona CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-542">How CORS works</span></span>

<span data-ttu-id="8b11f-543">En esta sección se describe lo que sucede en una solicitud de [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes http.</span><span class="sxs-lookup"><span data-stu-id="8b11f-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="8b11f-544">CORS **no** es una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="8b11f-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="8b11f-545">CORS es un estándar del W3C que permite a un servidor relajar la Directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="8b11f-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="8b11f-546">Por ejemplo, un actor malintencionado podría usar [impedir el scripting entre sitios (XSS)](xref:security/cross-site-scripting) en el sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.</span><span class="sxs-lookup"><span data-stu-id="8b11f-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="8b11f-547">La API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="8b11f-548">Es el cliente (explorador) el que debe aplicar CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="8b11f-549">El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="8b11f-550">Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:</span><span class="sxs-lookup"><span data-stu-id="8b11f-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="8b11f-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="8b11f-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="8b11f-552">Postman</span><span class="sxs-lookup"><span data-stu-id="8b11f-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="8b11f-553">HttpClient de .NET</span><span class="sxs-lookup"><span data-stu-id="8b11f-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="8b11f-554">Un explorador web escribiendo la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="8b11f-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="8b11f-555">Es una forma de que un servidor permita que los exploradores ejecuten una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) entre orígenes que, de lo contrario, se prohibirá.</span><span class="sxs-lookup"><span data-stu-id="8b11f-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="8b11f-556">Los exploradores (sin CORS) no pueden realizar solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="8b11f-557">Antes de CORS, se usaba [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) para eludir esta restricción.</span><span class="sxs-lookup"><span data-stu-id="8b11f-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="8b11f-558">JSONP no usa XHR, usa la `<script>` etiqueta para recibir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="8b11f-559">Los scripts pueden cargarse entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="8b11f-560">La [especificación CORS](https://www.w3.org/TR/cors/) presentó varios encabezados HTTP nuevos que permiten solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="8b11f-561">Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="8b11f-562">No es necesario el código personalizado de JavaScript para habilitar CORS.</span><span class="sxs-lookup"><span data-stu-id="8b11f-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="8b11f-563">A continuación se presenta un ejemplo de una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="8b11f-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="8b11f-564">El `Origin` encabezado proporciona el dominio del sitio que realiza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="8b11f-565">El `Origin` encabezado es obligatorio y debe ser diferente del host.</span><span class="sxs-lookup"><span data-stu-id="8b11f-565">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="8b11f-566">Si el servidor permite la solicitud, establece el `Access-Control-Allow-Origin` encabezado en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="8b11f-567">El valor de este encabezado coincide con el `Origin` encabezado de la solicitud o con el valor `"*"`comodín, lo que significa que se permite cualquier origen:</span><span class="sxs-lookup"><span data-stu-id="8b11f-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="8b11f-568">Si la respuesta no incluye el `Access-Control-Allow-Origin` encabezado, se produce un error en la solicitud de origen cruzado.</span><span class="sxs-lookup"><span data-stu-id="8b11f-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="8b11f-569">En concreto, el explorador no permite la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="8b11f-570">Aunque el servidor devuelva una respuesta correcta, el explorador no pone la respuesta a disposición de la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="8b11f-571">Prueba de CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-571">Test CORS</span></span>

<span data-ttu-id="8b11f-572">Para probar CORS:</span><span class="sxs-lookup"><span data-stu-id="8b11f-572">To test CORS:</span></span>

1. <span data-ttu-id="8b11f-573">[Cree un proyecto de API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="8b11f-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="8b11f-574">Como alternativa, puede [descargar el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="8b11f-575">Habilite CORS con uno de los enfoques de este documento.</span><span class="sxs-lookup"><span data-stu-id="8b11f-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="8b11f-576">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8b11f-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="8b11f-577">`WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar a la [descarga del código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="8b11f-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="8b11f-578">Cree un proyecto de aplicación WebRazor (páginas o MVC).</span><span class="sxs-lookup"><span data-stu-id="8b11f-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="8b11f-579">El ejemplo utiliza Razor páginas.</span><span class="sxs-lookup"><span data-stu-id="8b11f-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="8b11f-580">Puede crear la aplicación web en la misma solución que el proyecto de API.</span><span class="sxs-lookup"><span data-stu-id="8b11f-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="8b11f-581">Agregue el siguiente código resaltado al archivo *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8b11f-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="8b11f-582">En el código anterior, reemplace `url: 'https://<web app>.azurewebsites.net/api/values/1',` por la dirección URL a la aplicación implementada.</span><span class="sxs-lookup"><span data-stu-id="8b11f-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="8b11f-583">Implemente el proyecto de API.</span><span class="sxs-lookup"><span data-stu-id="8b11f-583">Deploy the API project.</span></span> <span data-ttu-id="8b11f-584">Por ejemplo, [implemente en Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="8b11f-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="8b11f-585">Ejecute las Razor páginas o la aplicación MVC desde el escritorio y haga clic en el botón **probar** .</span><span class="sxs-lookup"><span data-stu-id="8b11f-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="8b11f-586">Use las herramientas F12 para revisar los mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="8b11f-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="8b11f-587">Quite el origen localhost de `WithOrigins` e implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="8b11f-588">Como alternativa, ejecute la aplicación cliente con un puerto diferente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="8b11f-589">Por ejemplo, ejecute desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8b11f-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="8b11f-590">Pruebe con la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="8b11f-590">Test with the client app.</span></span> <span data-ttu-id="8b11f-591">Los errores de CORS devuelven un error, pero el mensaje de error no está disponible para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8b11f-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="8b11f-592">Use la pestaña consola de las herramientas F12 para ver el error.</span><span class="sxs-lookup"><span data-stu-id="8b11f-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="8b11f-593">Dependiendo del explorador, obtendrá un error (en la consola de herramientas de F12) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="8b11f-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="8b11f-594">Uso de Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="8b11f-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="8b11f-595">**SEC7120: [CORS] el origen `https://localhost:44375` no se encontró `https://localhost:44375` en el encabezado de respuesta Access-Control-Allow-Origin para el recurso entre orígenes en`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="8b11f-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="8b11f-596">Usar Chrome:</span><span class="sxs-lookup"><span data-stu-id="8b11f-596">Using Chrome:</span></span>

     <span data-ttu-id="8b11f-597">**La directiva `https://webapi.azurewebsites.net/api/values/1` de CORS ha bloqueado `https://localhost:44375` el acceso a XMLHttpRequest desde el origen: no hay ningún encabezado "Access-Control-Allow-Origin" en el recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="8b11f-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="8b11f-598">Los puntos de conexión habilitados para CORS se pueden probar con una herramienta, como [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="8b11f-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="8b11f-599">Cuando se usa una herramienta, el origen de la solicitud especificada por `Origin` el encabezado debe ser diferente del host que recibe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="8b11f-600">Si la solicitud no es de *origen cruzado* según el valor del `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="8b11f-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="8b11f-601">No es necesario que el middleware de CORS procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="8b11f-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="8b11f-602">Los encabezados CORS no se devuelven en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8b11f-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="8b11f-603">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="8b11f-603">CORS in IIS</span></span>

<span data-ttu-id="8b11f-604">Al implementar en IIS, CORS debe ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="8b11f-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="8b11f-605">Para admitir este escenario, es necesario instalar y configurar el [módulo IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8b11f-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b11f-606">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8b11f-606">Additional resources</span></span>

* [<span data-ttu-id="8b11f-607">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="8b11f-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="8b11f-608">Introducción al módulo IIS CORS</span><span class="sxs-lookup"><span data-stu-id="8b11f-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
