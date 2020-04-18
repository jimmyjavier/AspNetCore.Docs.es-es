---
title: Habilitar solicitudes entre orígenes (CORS) en ASP.NET Core
author: rick-anderson
description: Obtén información sobre cómo CORS como estándar para permitir o rechazar solicitudes entre orígenes en una aplicación ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642702"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="e0520-103">Habilitar solicitudes entre orígenes (CORS) en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e0520-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e0520-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y Kirk [Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e0520-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="e0520-105">En este artículo se muestra cómo habilitar CORS en una aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0520-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e0520-106">La seguridad del explorador impide que una página web realice solicitudes a un dominio diferente al que sirvió a la página web.</span><span class="sxs-lookup"><span data-stu-id="e0520-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e0520-107">Esta restricción se denomina *directiva del mismo origen.*</span><span class="sxs-lookup"><span data-stu-id="e0520-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e0520-108">La directiva del mismo origen impide que un sitio malintencionado lea datos confidenciales de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="e0520-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e0520-109">A veces, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="e0520-110">Para obtener más información, consulte el [artículo de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="e0520-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e0520-111">Uso compartido de recursos entre [orígenes](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="e0520-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e0520-112">Es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e0520-113">**No** es una característica de seguridad, CORS relaja la seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0520-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e0520-114">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e0520-115">Para obtener más información, consulte [Cómo funciona CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="e0520-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e0520-116">Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras rechaza otras.</span><span class="sxs-lookup"><span data-stu-id="e0520-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e0520-117">Es más seguro y flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="e0520-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e0520-118">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e0520-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e0520-119">Mismo origen</span><span class="sxs-lookup"><span data-stu-id="e0520-119">Same origin</span></span>

<span data-ttu-id="e0520-120">Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="e0520-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e0520-121">Estas dos direcciones URL tienen el mismo origen:</span><span class="sxs-lookup"><span data-stu-id="e0520-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e0520-122">Estas direcciones URL tienen orígenes diferentes a los dos URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="e0520-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e0520-123">`https://example.net`&ndash; Dominio diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="e0520-124">`https://www.example.com/foo.html`&ndash; Subdominio diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="e0520-125">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="e0520-126">`https://example.com:9000/foo.html`&ndash; Puerto diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="e0520-127">Habilitación de CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-127">Enable CORS</span></span>

<span data-ttu-id="e0520-128">Hay tres maneras de habilitar CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="e0520-129">En middleware mediante una [directiva con nombre](#np) o una directiva [predeterminada.](#dp)</span><span class="sxs-lookup"><span data-stu-id="e0520-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="e0520-130">Uso del [enrutamiento de punto final](#ecors).</span><span class="sxs-lookup"><span data-stu-id="e0520-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="e0520-131">Con el atributo [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="e0520-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="e0520-132">El uso del atributo [[EnableCors]](#attr) con una directiva con nombre proporciona el control más preciso para limitar los puntos de conexión que admiten CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="e0520-133">Cada enfoque se detalla en las secciones siguientes.</span><span class="sxs-lookup"><span data-stu-id="e0520-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e0520-134">CORS con política y middleware con nombre</span><span class="sxs-lookup"><span data-stu-id="e0520-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="e0520-135">CORS Middleware controla las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e0520-136">El código siguiente aplica una directiva CORS a todos los puntos de conexión de la aplicación con los orígenes especificados:</span><span class="sxs-lookup"><span data-stu-id="e0520-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="e0520-137">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e0520-137">The preceding code:</span></span>

* <span data-ttu-id="e0520-138">Establece el nombre `_myAllowSpecificOrigins`de la directiva en .</span><span class="sxs-lookup"><span data-stu-id="e0520-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="e0520-139">El nombre de la directiva es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="e0520-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e0520-140">Llama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> al método de `_myAllowSpecificOrigins` extensión y especifica la directiva CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="e0520-141">`UseCors`agrega el middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="e0520-142">La llamada `UseCors` a ser `UseRouting`colocada `UseAuthorization`después de , pero antes .</span><span class="sxs-lookup"><span data-stu-id="e0520-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="e0520-143">Para obtener más información, consulte Pedido de [Middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="e0520-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="e0520-144">Llamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="e0520-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e0520-145">La expresión <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda toma un objeto.</span><span class="sxs-lookup"><span data-stu-id="e0520-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e0520-146">[Las opciones](#cors-policy-options)de `WithOrigins`configuración, como , se describen más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="e0520-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="e0520-147">Habilita `_myAllowSpecificOrigins` la directiva CORS para todos los extremos del controlador.</span><span class="sxs-lookup"><span data-stu-id="e0520-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="e0520-148">Consulte Enrutamiento de [puntos](#ecors) de conexión para aplicar una directiva CORS a puntos de conexión específicos.</span><span class="sxs-lookup"><span data-stu-id="e0520-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="e0520-149">Con el enrutamiento de ***must*** punto final, el middleware `UseRouting` CORS debe configurarse para ejecutarse entre las llamadas a y `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="e0520-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="e0520-150">Consulte [Probar CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="e0520-151">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega servicios CORS al contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e0520-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e0520-152">Para obtener más información, consulte Opciones de [directiva corS](#cpo) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e0520-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="e0520-153">Los <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> métodos se pueden encadenar, como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0520-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="e0520-154">Nota: La dirección URL especificada **no** debe`/`contener una barra diagonal final ( ).</span><span class="sxs-lookup"><span data-stu-id="e0520-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e0520-155">Si la dirección `/`URL termina `false` con , se devuelve la comparación y no se devuelve ningún encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="e0520-156">CORS con política y middleware predeterminados</span><span class="sxs-lookup"><span data-stu-id="e0520-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="e0520-157">El siguiente código resaltado habilita la directiva CORS predeterminada:</span><span class="sxs-lookup"><span data-stu-id="e0520-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="e0520-158">El código anterior aplica la directiva CORS predeterminada a todos los extremos del controlador.</span><span class="sxs-lookup"><span data-stu-id="e0520-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="e0520-159">Habilitación de CORS con enrutamiento de punto de conexión</span><span class="sxs-lookup"><span data-stu-id="e0520-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="e0520-160">Habilitar CORS por punto final `RequireCors` mediante actualmente ***no*** admite [solicitudes de comprobación previa automáticas.](#apf)</span><span class="sxs-lookup"><span data-stu-id="e0520-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="e0520-161">Para obtener más información, consulte este problema de [GitHub](https://github.com/dotnet/aspnetcore/issues/20709) y Probar CORS con enrutamiento de puntos de conexión [y [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="e0520-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="e0520-162">Con el enrutamiento de punto final, CORS se <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> puede habilitar por punto final mediante el conjunto de métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="e0520-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="e0520-163">En el código anterior:</span><span class="sxs-lookup"><span data-stu-id="e0520-163">In the preceding code:</span></span>

* <span data-ttu-id="e0520-164">`app.UseCors`habilita el middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="e0520-165">Dado que no se ha `app.UseCors()` configurado una directiva predeterminada, no habilita CORS por sí sola.</span><span class="sxs-lookup"><span data-stu-id="e0520-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="e0520-166">Los `/echo` puntos de conexión y los puntos de conexión de controlador permiten solicitudes entre orígenes mediante la directiva especificada.</span><span class="sxs-lookup"><span data-stu-id="e0520-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="e0520-167">Los `/echo2` puntos de conexión y Razor Pages ***no*** permiten solicitudes entre orígenes porque no se especificó ninguna directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e0520-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="e0520-168">El atributo [[DisableCors]](#dc) ***no*** deshabilita CORS que ha `RequireCors`sido habilitado por el enrutamiento de punto final con .</span><span class="sxs-lookup"><span data-stu-id="e0520-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="e0520-169">Consulte Probar CORS con enrutamiento de punto de conexión [y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar código similar al anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e0520-170">Habilitar CORS con atributos</span><span class="sxs-lookup"><span data-stu-id="e0520-170">Enable CORS with attributes</span></span>

<span data-ttu-id="e0520-171">Habilitar CORS con el atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) y aplicar una directiva con nombre solo a los puntos de conexión que requieren CORS proporciona el mejor control.</span><span class="sxs-lookup"><span data-stu-id="e0520-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="e0520-172">El atributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="e0520-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e0520-173">El `[EnableCors]` atributo habilita CORS para los puntos finales seleccionados, en lugar de todos los puntos finales:</span><span class="sxs-lookup"><span data-stu-id="e0520-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="e0520-174">`[EnableCors]`especifica la directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e0520-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="e0520-175">`[EnableCors("{Policy String}")]`especifica una directiva con nombre.</span><span class="sxs-lookup"><span data-stu-id="e0520-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="e0520-176">El `[EnableCors]` atributo se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="e0520-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="e0520-177">Página de Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="e0520-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="e0520-178">Controlador</span><span class="sxs-lookup"><span data-stu-id="e0520-178">Controller</span></span>
* <span data-ttu-id="e0520-179">Método de acción del controlador</span><span class="sxs-lookup"><span data-stu-id="e0520-179">Controller action method</span></span>

<span data-ttu-id="e0520-180">Se pueden aplicar diferentes directivas a controladores, `[EnableCors]` modelos de página o métodos de acción con el atributo.</span><span class="sxs-lookup"><span data-stu-id="e0520-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="e0520-181">Cuando `[EnableCors]` el atributo se aplica a un controlador, modelo de página o método de acción y CORS está habilitado en middleware, se aplican ***ambas*** directivas.</span><span class="sxs-lookup"><span data-stu-id="e0520-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="e0520-182">***Se recomienda no combinar directivas. Use el*** `[EnableCors]` ***atributo o middleware, no ambos en la misma aplicación.***</span><span class="sxs-lookup"><span data-stu-id="e0520-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="e0520-183">El código siguiente aplica una directiva diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="e0520-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e0520-184">El código siguiente crea dos directivas CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="e0520-185">Para el mejor control de las solicitudes CORS limitantes:</span><span class="sxs-lookup"><span data-stu-id="e0520-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="e0520-186">Utilícelo `[EnableCors("MyPolicy")]` con una directiva con nombre.</span><span class="sxs-lookup"><span data-stu-id="e0520-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="e0520-187">No defina una directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e0520-187">Don't define a default policy.</span></span>
* <span data-ttu-id="e0520-188">No utilice el enrutamiento de [punto](#ecors)final.</span><span class="sxs-lookup"><span data-stu-id="e0520-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="e0520-189">El código de la siguiente sección cumple con la lista anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="e0520-190">Consulte [Probar CORS](#testc) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="e0520-191">Desactivar CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-191">Disable CORS</span></span>

<span data-ttu-id="e0520-192">El atributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***no*** deshabilita CORS habilitado por el enrutamiento de [punto final.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="e0520-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="e0520-193">El código siguiente define `"MyPolicy"`la directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="e0520-194">El código siguiente deshabilita CORS para la `GetValues2` acción:</span><span class="sxs-lookup"><span data-stu-id="e0520-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="e0520-195">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e0520-195">The preceding code:</span></span>

* <span data-ttu-id="e0520-196">No habilita CORS con enrutamiento de [punto](#ecors)final.</span><span class="sxs-lookup"><span data-stu-id="e0520-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="e0520-197">No define una [directiva CORS predeterminada.](#dp)</span><span class="sxs-lookup"><span data-stu-id="e0520-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="e0520-198">Utiliza [[EnableCors("MyPolicy")]](#attr) para `"MyPolicy"` habilitar la directiva CORS para el controlador.</span><span class="sxs-lookup"><span data-stu-id="e0520-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="e0520-199">Deshabilita CORS para `GetValues2` el método.</span><span class="sxs-lookup"><span data-stu-id="e0520-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="e0520-200">Consulte [Probar CORS](#testc) para obtener instrucciones sobre cómo probar el código anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e0520-201">Opciones de política CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-201">CORS policy options</span></span>

<span data-ttu-id="e0520-202">Esta sección describe las diversas opciones que se pueden fijar en una directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e0520-203">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e0520-204">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e0520-205">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e0520-206">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="e0520-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e0520-207">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="e0520-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e0520-208">Establecer el tiempo de expiración de la comprobación preliminar</span><span class="sxs-lookup"><span data-stu-id="e0520-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e0520-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama `Startup.ConfigureServices`en .</span><span class="sxs-lookup"><span data-stu-id="e0520-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e0520-210">Para algunas opciones, puede ser útil leer primero la sección [Cómo funciona CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="e0520-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e0520-211">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-211">Set the allowed origins</span></span>

<span data-ttu-id="e0520-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitudes CORS de todos los`http` `https`orígenes con cualquier esquema ( o ).</span><span class="sxs-lookup"><span data-stu-id="e0520-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e0520-213">`AllowAnyOrigin`es inseguro porque *cualquier sitio web* puede hacer solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e0520-214">Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración insegura y puede dar lugar a la falsificación de solicitudes entre sitios.</span><span class="sxs-lookup"><span data-stu-id="e0520-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e0520-215">El servicio CORS devuelve una respuesta CORS no válida cuando una aplicación está configurada con ambos métodos.</span><span class="sxs-lookup"><span data-stu-id="e0520-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="e0520-216">`AllowAnyOrigin`afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Origin` encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e0520-217">Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e0520-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e0520-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Establece <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propiedad de la directiva como una función que permite que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e0520-219">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e0520-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e0520-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e0520-221">Permite cualquier método HTTP:</span><span class="sxs-lookup"><span data-stu-id="e0520-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="e0520-222">Afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Methods` encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e0520-223">Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e0520-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e0520-224">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-224">Set the allowed request headers</span></span>

<span data-ttu-id="e0520-225">Para permitir que se envíen encabezados específicos en una solicitud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, [denominadaencabezados](https://xhr.spec.whatwg.org/#request)de solicitud de autor, llame y especifique los encabezados permitidos:</span><span class="sxs-lookup"><span data-stu-id="e0520-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="e0520-226">Para permitir todos los encabezados de solicitud de [autor,](https://www.w3.org/TR/cors/#author-request-headers)llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="e0520-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="e0520-227">`AllowAnyHeader`afecta a las solicitudes de comprobación previa y el encabezado [Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="e0520-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="e0520-228">Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e0520-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e0520-229">Una directiva de Middleware de CORS `WithHeaders` coincide con los encabezados `Access-Control-Request-Headers` específicos especificados por `WithHeaders`sólo es posible cuando los encabezados enviados exactamente coinciden con los encabezados indicados en .</span><span class="sxs-lookup"><span data-stu-id="e0520-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="e0520-230">Por ejemplo, considere una aplicación configurada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e0520-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="e0520-231">CorS Middleware rechaza una solicitud de comprobación `Content-Language` previa con el siguiente encabezado de `WithHeaders`solicitud porque ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) no aparece en:</span><span class="sxs-lookup"><span data-stu-id="e0520-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="e0520-232">La aplicación devuelve una respuesta *200 OK,* pero no devuelve los encabezados CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e0520-233">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e0520-234">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="e0520-234">Set the exposed response headers</span></span>

<span data-ttu-id="e0520-235">De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e0520-236">Para obtener más información, consulte Uso compartido de recursos entre orígenes [(terminología): Encabezado](https://www.w3.org/TR/cors/#simple-response-header)de respuesta simple .</span><span class="sxs-lookup"><span data-stu-id="e0520-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e0520-237">Los encabezados de respuesta que están disponibles de forma predeterminada son:</span><span class="sxs-lookup"><span data-stu-id="e0520-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e0520-238">La especificación CORS llama a estos *encabezados de respuesta simples.*</span><span class="sxs-lookup"><span data-stu-id="e0520-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e0520-239">Para que otros encabezados estén <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>disponibles para la aplicación, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e0520-240">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="e0520-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e0520-241">Las credenciales requieren un control especial en una solicitud CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e0520-242">De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e0520-243">Las credenciales incluyen cookies y esquemas de autenticación HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0520-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e0520-244">Para enviar credenciales con una solicitud entre `XMLHttpRequest.withCredentials` orígenes, el cliente debe establecerse en `true`.</span><span class="sxs-lookup"><span data-stu-id="e0520-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e0520-245">Usando `XMLHttpRequest` directamente:</span><span class="sxs-lookup"><span data-stu-id="e0520-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e0520-246">Uso de jQuery:</span><span class="sxs-lookup"><span data-stu-id="e0520-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e0520-247">Uso de la [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="e0520-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e0520-248">El servidor debe permitir las credenciales.</span><span class="sxs-lookup"><span data-stu-id="e0520-248">The server must allow the credentials.</span></span> <span data-ttu-id="e0520-249">Para permitir credenciales entre <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>orígenes, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="e0520-250">La respuesta HTTP `Access-Control-Allow-Credentials` incluye un encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e0520-251">Si el explorador envía credenciales pero la `Access-Control-Allow-Credentials` respuesta no incluye un encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e0520-252">Permitir credenciales entre orígenes es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0520-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e0520-253">Un sitio web de otro dominio puede enviar las credenciales de un usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="e0520-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e0520-254">La especificación CORS también indica `"*"` que establecer los orígenes `Access-Control-Allow-Credentials` en (todos los orígenes) no es válido si el encabezado está presente.</span><span class="sxs-lookup"><span data-stu-id="e0520-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="e0520-255">Solicitudes de comprobación previa</span><span class="sxs-lookup"><span data-stu-id="e0520-255">Preflight requests</span></span>

<span data-ttu-id="e0520-256">Para algunas solicitudes CORS, el explorador envía una solicitud [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) adicional antes de realizar la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="e0520-257">Esta solicitud se denomina solicitud de [comprobación previa.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="e0520-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="e0520-258">El navegador puede omitir la solicitud de comprobación previa si se cumplen ***todas*** las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="e0520-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="e0520-259">El método de solicitud es GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="e0520-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e0520-260">La aplicación no establece encabezados `Accept`de `Accept-Language` `Content-Language`solicitud `Content-Type`que `Last-Event-ID`no sean , , , o .</span><span class="sxs-lookup"><span data-stu-id="e0520-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e0520-261">El `Content-Type` encabezado, si se establece, tiene uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="e0520-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e0520-262">La regla en los encabezados de solicitud establecidos para la `setRequestHeader` solicitud `XMLHttpRequest` de cliente se aplica a los encabezados que establece la aplicación mediante una llamada en el objeto.</span><span class="sxs-lookup"><span data-stu-id="e0520-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e0520-263">La especificación CORS llama a estos encabezados de solicitud de autor de [encabezados](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="e0520-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="e0520-264">La regla no se aplica a los encabezados `User-Agent` `Host`que `Content-Length`el explorador puede establecer, como , , o .</span><span class="sxs-lookup"><span data-stu-id="e0520-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e0520-265">A continuación se muestra una respuesta de ejemplo similar a la solicitud de comprobación previa realizada desde el botón **[Put test]** en la sección [Test CORS](#testc) de este documento.</span><span class="sxs-lookup"><span data-stu-id="e0520-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="e0520-266">La solicitud de comprobación previa utiliza el método [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="e0520-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="e0520-267">Puede incluir los siguientes encabezados:</span><span class="sxs-lookup"><span data-stu-id="e0520-267">It may include the following headers:</span></span>

* <span data-ttu-id="e0520-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): El método HTTP que se utilizará para la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e0520-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Una lista de encabezados de solicitud que la aplicación establece en la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e0520-270">Como se indicó anteriormente, esto no incluye encabezados `User-Agent`que el explorador establece, como .</span><span class="sxs-lookup"><span data-stu-id="e0520-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="e0520-271">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="e0520-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="e0520-272">Si se deniega la solicitud de `200 OK` comprobación preliminar, la aplicación devuelve una respuesta pero no establece los encabezados CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="e0520-273">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="e0520-274">Para obtener un ejemplo de una solicitud de comprobación previa denegada, consulte la sección [Test CORS](#testc) de este documento.</span><span class="sxs-lookup"><span data-stu-id="e0520-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="e0520-275">Con las herramientas F12, la aplicación de consola muestra un error similar a uno de los siguientes, dependiendo del explorador:</span><span class="sxs-lookup"><span data-stu-id="e0520-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="e0520-276">Firefox: Solicitud entre orígenes bloqueada: la misma directiva de `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`origen no permite leer el recurso remoto en .</span><span class="sxs-lookup"><span data-stu-id="e0520-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="e0520-277">(Motivo: la solicitud CORS no se realizó correctamente).</span><span class="sxs-lookup"><span data-stu-id="e0520-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="e0520-278">Más información</span><span class="sxs-lookup"><span data-stu-id="e0520-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="e0520-279">Basado en cromo: Elhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5acceso ahttps://cors3.azurewebsites.netla captura en ' ' desde el origen ' ' ha sido bloqueado por la directiva CORS: La respuesta a la solicitud de comprobación previa no pasa la comprobación de control de acceso: No hay encabezado 'Access-Control-Allow-Origin' en el recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="e0520-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="e0520-280">Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="e0520-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="e0520-281">Para permitir encabezados <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>específicos, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="e0520-282">Para permitir todos los encabezados de solicitud de [autor,](https://www.w3.org/TR/cors/#author-request-headers)llame a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="e0520-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="e0520-283">Los navegadores no son `Access-Control-Request-Headers`coherentes en la forma en que establecen .</span><span class="sxs-lookup"><span data-stu-id="e0520-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e0520-284">Si cualquiera de los dos:</span><span class="sxs-lookup"><span data-stu-id="e0520-284">If either:</span></span>

* <span data-ttu-id="e0520-285">Los encabezados se establecen en cualquier otra cosa que no sea`"*"`</span><span class="sxs-lookup"><span data-stu-id="e0520-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="e0520-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se llama: Incluir `Accept` `Content-Type`al `Origin`menos , , y , además de los encabezados personalizados que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="e0520-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="e0520-287">Código de solicitud de comprobación previa automática</span><span class="sxs-lookup"><span data-stu-id="e0520-287">Automatic preflight request code</span></span>

<span data-ttu-id="e0520-288">Cuando se aplica la directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="e0520-289">Globalmente `app.UseCors` llamando `Startup.Configure`a .</span><span class="sxs-lookup"><span data-stu-id="e0520-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="e0520-290">Usando `[EnableCors]` el atributo.</span><span class="sxs-lookup"><span data-stu-id="e0520-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="e0520-291">ASP.NET Core responde a la solicitud OPTIONS de comprobación preliminar.</span><span class="sxs-lookup"><span data-stu-id="e0520-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="e0520-292">Habilitar CORS por punto final `RequireCors` mediante actualmente ***no*** admite solicitudes de comprobación previa automáticas.</span><span class="sxs-lookup"><span data-stu-id="e0520-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="e0520-293">La sección [de prueba CORS](#testc) de este documento demuestra este comportamiento.</span><span class="sxs-lookup"><span data-stu-id="e0520-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="e0520-294">Atributo [HttpOptions] para solicitudes de comprobación previa</span><span class="sxs-lookup"><span data-stu-id="e0520-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="e0520-295">Cuando CORS está habilitado con la directiva adecuada, ASP.NET Core suele responder automáticamente a las solicitudes de comprobación previa de CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="e0520-296">En algunos escenarios, este puede no ser el caso.</span><span class="sxs-lookup"><span data-stu-id="e0520-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="e0520-297">Por ejemplo, el uso de [CORS con el enrutamiento](#ecors)de punto final .</span><span class="sxs-lookup"><span data-stu-id="e0520-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="e0520-298">El código siguiente utiliza el atributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) para crear extremos para las solicitudes OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="e0520-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="e0520-299">Consulte Probar CORS con enrutamiento de puntos de conexión [y [HttpOptions]](#tcer) para obtener instrucciones sobre cómo probar el código anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e0520-300">Establecer el tiempo de expiración de la comprobación preliminar</span><span class="sxs-lookup"><span data-stu-id="e0520-300">Set the preflight expiration time</span></span>

<span data-ttu-id="e0520-301">El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud de comprobación previa.</span><span class="sxs-lookup"><span data-stu-id="e0520-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e0520-302">Para establecer este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>encabezado, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e0520-303">Cómo funciona CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-303">How CORS works</span></span>

<span data-ttu-id="e0520-304">En esta sección se describe lo que sucede en una solicitud [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0520-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e0520-305">CORS **no** es una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0520-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="e0520-306">CORS es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e0520-307">Por ejemplo, un actor malintencionado podría usar Secuencias de comandos entre sitios [(XSS)](xref:security/cross-site-scripting) en su sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.</span><span class="sxs-lookup"><span data-stu-id="e0520-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e0520-308">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="e0520-309">Depende del cliente (navegador) aplicar CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e0520-310">El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e0520-311">Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:</span><span class="sxs-lookup"><span data-stu-id="e0520-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e0520-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e0520-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e0520-313">Postman</span><span class="sxs-lookup"><span data-stu-id="e0520-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e0520-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="e0520-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e0520-315">Un explorador web introduciendo la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="e0520-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e0520-316">Es una forma de que un servidor permita a los navegadores ejecutar una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) de origen cruzado que, de lo contrario, estaría prohibida.</span><span class="sxs-lookup"><span data-stu-id="e0520-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e0520-317">Los navegadores sin CORS no pueden realizar solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="e0520-318">Antes de CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) se usaba para eludir esta restricción.</span><span class="sxs-lookup"><span data-stu-id="e0520-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e0520-319">JSONP no usa XHR, usa `<script>` la etiqueta para recibir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e0520-320">Los scripts se pueden cargar entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e0520-321">La [especificación CORS](https://www.w3.org/TR/cors/) introdujo varios encabezados HTTP nuevos que habilitan las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e0520-322">Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e0520-323">El código JavaScript personalizado no es necesario para habilitar CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e0520-324">El [botón de prueba PUT](https://cors3.azurewebsites.net/test) en el [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) implementado</span><span class="sxs-lookup"><span data-stu-id="e0520-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="e0520-325">A continuación se muestra un ejemplo de [Values](https://cors3.azurewebsites.net/) una solicitud `https://cors1.azurewebsites.net/api/values`entre orígenes desde el botón de prueba Valores a .</span><span class="sxs-lookup"><span data-stu-id="e0520-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="e0520-326">El `Origin` encabezado:</span><span class="sxs-lookup"><span data-stu-id="e0520-326">The `Origin` header:</span></span>

* <span data-ttu-id="e0520-327">Proporciona el dominio del sitio que realiza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="e0520-328">Es necesario y debe ser diferente del host.</span><span class="sxs-lookup"><span data-stu-id="e0520-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="e0520-329">**Encabezados generales**</span><span class="sxs-lookup"><span data-stu-id="e0520-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="e0520-330">**Encabezados de respuesta**</span><span class="sxs-lookup"><span data-stu-id="e0520-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="e0520-331">**Encabezados de solicitud**</span><span class="sxs-lookup"><span data-stu-id="e0520-331">**Request headers**</span></span>

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

<span data-ttu-id="e0520-332">En `OPTIONS` las solicitudes, el servidor establece el encabezado **Response headers** `Access-Control-Allow-Origin: {allowed origin}` en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="e0520-333">Por ejemplo, la solicitud de [botón](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) `OPTIONS` de ejemplo implementada, [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) contiene los siguientes encabezados:</span><span class="sxs-lookup"><span data-stu-id="e0520-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="e0520-334">**Encabezados generales**</span><span class="sxs-lookup"><span data-stu-id="e0520-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="e0520-335">**Encabezados de respuesta**</span><span class="sxs-lookup"><span data-stu-id="e0520-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="e0520-336">**Encabezados de solicitud**</span><span class="sxs-lookup"><span data-stu-id="e0520-336">**Request headers**</span></span>

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

<span data-ttu-id="e0520-337">En los **encabezados Response anteriores,** el servidor establece el encabezado [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="e0520-338">El `https://cors1.azurewebsites.net` valor de este `Origin` encabezado coincide con el encabezado de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="e0520-339">Si <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> se llama, se devuelve el `Access-Control-Allow-Origin: *`valor comodín .</span><span class="sxs-lookup"><span data-stu-id="e0520-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="e0520-340">`AllowAnyOrigin`permite cualquier origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="e0520-341">Si la respuesta no `Access-Control-Allow-Origin` incluye el encabezado, se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e0520-342">Específicamente, el navegador no permite la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e0520-343">Incluso si el servidor devuelve una respuesta correcta, el explorador no hace que la respuesta esté disponible para la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="e0520-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="e0520-344">Mostrar solicitudes OPTIONS</span><span class="sxs-lookup"><span data-stu-id="e0520-344">Display OPTIONS requests</span></span>

<span data-ttu-id="e0520-345">De forma predeterminada, los navegadores Chrome y Edge no muestran solicitudes OPTIONS en la pestaña de red de las herramientas F12.</span><span class="sxs-lookup"><span data-stu-id="e0520-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="e0520-346">Para mostrar solicitudes OPTIONS en estos navegadores:</span><span class="sxs-lookup"><span data-stu-id="e0520-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="e0520-347">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="e0520-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="e0520-348">desactivar la bandera.</span><span class="sxs-lookup"><span data-stu-id="e0520-348">disable the flag.</span></span>
* <span data-ttu-id="e0520-349">Reiniciar.</span><span class="sxs-lookup"><span data-stu-id="e0520-349">restart.</span></span>

<span data-ttu-id="e0520-350">Firefox muestra las solicitudes OPTIONS de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e0520-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="e0520-351">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="e0520-351">CORS in IIS</span></span>

<span data-ttu-id="e0520-352">Al implementar en IIS, CORS tiene que ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="e0520-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="e0520-353">Para admitir este escenario, el [módulo CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) de IIS debe instalarse y configurarse para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="e0520-354">Prueba de CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-354">Test CORS</span></span>

<span data-ttu-id="e0520-355">La [descarga de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) tiene código para probar CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="e0520-356">Vea [cómo descargarlo](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e0520-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="e0520-357">El ejemplo es un proyecto de API con Razor Pages agregado:</span><span class="sxs-lookup"><span data-stu-id="e0520-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="e0520-358">`WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar al código de ejemplo de [descarga.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)</span><span class="sxs-lookup"><span data-stu-id="e0520-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="e0520-359">A `ValuesController` continuación se proporcionan los puntos de conexión para las pruebas:</span><span class="sxs-lookup"><span data-stu-id="e0520-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="e0520-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) lo proporciona el paquete NuGet [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) y muestra información de ruta.</span><span class="sxs-lookup"><span data-stu-id="e0520-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="e0520-361">Pruebe el código de ejemplo anterior mediante uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="e0520-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="e0520-362">Use la aplicación de [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)ejemplo implementada en .</span><span class="sxs-lookup"><span data-stu-id="e0520-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="e0520-363">No es necesario descargar el ejemplo.</span><span class="sxs-lookup"><span data-stu-id="e0520-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="e0520-364">Ejecute el `dotnet run` ejemplo con la `https://localhost:5001`dirección URL predeterminada de .</span><span class="sxs-lookup"><span data-stu-id="e0520-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="e0520-365">Ejecute el ejemplo de Visual Studio con el puerto establecido `https://localhost:44398`en 44398 para una dirección URL de .</span><span class="sxs-lookup"><span data-stu-id="e0520-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="e0520-366">Uso de un navegador con las herramientas F12:</span><span class="sxs-lookup"><span data-stu-id="e0520-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="e0520-367">Seleccione el botón **Valores** y revise los encabezados en la pestaña **Red.**</span><span class="sxs-lookup"><span data-stu-id="e0520-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="e0520-368">Seleccione el botón **PUT test.**</span><span class="sxs-lookup"><span data-stu-id="e0520-368">Select the **PUT test** button.</span></span> <span data-ttu-id="e0520-369">Consulte Solicitudes de opciones de [visualización](#options) para obtener instrucciones sobre cómo mostrar la solicitud OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="e0520-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="e0520-370">La **prueba PUT** crea dos solicitudes, una solicitud de comprobación previa OPTIONS y la solicitud PUT.</span><span class="sxs-lookup"><span data-stu-id="e0520-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="e0520-371">Seleccione **`GetValues2 [DisableCors]`** el botón para desencadenar una solicitud CORS con errores.</span><span class="sxs-lookup"><span data-stu-id="e0520-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="e0520-372">Como se menciona en el documento, la respuesta devuelve 200 éxito, pero no se realiza la solicitud CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="e0520-373">Seleccione la pestaña **Consola** para ver el error CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="e0520-374">Dependiendo del navegador, se muestra un error similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0520-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="e0520-375">El acceso `'https://cors1.azurewebsites.net/api/values/GetValues2'` a `'https://cors3.azurewebsites.net'` la captura en origen se ha bloqueado mediante la directiva CORS: No hay ningún encabezado 'Access-Control-Allow-Origin' en el recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="e0520-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="e0520-376">Si una respuesta opaca sirve a sus necesidades, establezca el modo de la solicitud en 'no-cors' para obtener el recurso con CORS deshabilitado.</span><span class="sxs-lookup"><span data-stu-id="e0520-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="e0520-377">Los puntos finales habilitados para CORS se pueden probar con una herramienta, como [curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)o [Postman.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="e0520-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e0520-378">Cuando se utiliza una herramienta, el origen `Origin` de la solicitud especificada por el encabezado debe diferir del host que recibe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e0520-379">Si la solicitud no es *de origen* cruzado `Origin` en función del valor del encabezado:</span><span class="sxs-lookup"><span data-stu-id="e0520-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e0520-380">No es necesario que CORS Middleware procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e0520-381">Los encabezados CORS no se devuelven en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="e0520-382">El siguiente `curl` comando utiliza para emitir una solicitud OPTIONS con información:</span><span class="sxs-lookup"><span data-stu-id="e0520-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="e0520-383">Pruebe CORS con enrutamiento de punto final y [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="e0520-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="e0520-384">Habilitar CORS por punto final `RequireCors` mediante actualmente ***no*** admite [solicitudes de comprobación previa automáticas.](#apf)</span><span class="sxs-lookup"><span data-stu-id="e0520-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="e0520-385">Considere el código siguiente que utiliza el enrutamiento de [punto sensal para habilitar CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="e0520-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="e0520-386">A `TodoItems1Controller` continuación se proporcionan puntos de conexión para las pruebas:</span><span class="sxs-lookup"><span data-stu-id="e0520-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="e0520-387">Pruebe el código anterior de la página de [prueba](https://cors1.azurewebsites.net/test?number=1) del [ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)implementado.</span><span class="sxs-lookup"><span data-stu-id="e0520-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="e0520-388">Los **botones Delete [EnableCors]** y **GET [EnableCors]** se realizan correctamente, porque los puntos de conexión tienen `[EnableCors]` y responden a las solicitudes de comprobación previa.</span><span class="sxs-lookup"><span data-stu-id="e0520-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="e0520-389">Se produce un error en los otros puntos de conexión.</span><span class="sxs-lookup"><span data-stu-id="e0520-389">The other endpoints fails.</span></span> <span data-ttu-id="e0520-390">Se produce un error en el botón **GET,** porque [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) envía:</span><span class="sxs-lookup"><span data-stu-id="e0520-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="e0520-391">A `TodoItems2Controller` continuación se proporcionan puntos de conexión similares, pero incluye código explícito para responder a las solicitudes OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="e0520-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="e0520-392">Pruebe el código anterior de la página de [prueba](https://cors1.azurewebsites.net/test?number=2) del ejemplo implementado.</span><span class="sxs-lookup"><span data-stu-id="e0520-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="e0520-393">En la lista desplegable **Controlador,** seleccione **Comprobación previa** y, a continuación, **Establecer controlador**.</span><span class="sxs-lookup"><span data-stu-id="e0520-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="e0520-394">Todas las llamadas `TodoItems2Controller` CORS a los puntos de conexión se realizan correctamente.</span><span class="sxs-lookup"><span data-stu-id="e0520-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0520-395">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e0520-395">Additional resources</span></span>

* [<span data-ttu-id="e0520-396">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="e0520-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="e0520-397">Introducción al módulo CorS de IIS</span><span class="sxs-lookup"><span data-stu-id="e0520-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e0520-398">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0520-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0520-399">En este artículo se muestra cómo habilitar CORS en una aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e0520-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e0520-400">La seguridad del explorador impide que una página web realice solicitudes a un dominio diferente al que sirvió a la página web.</span><span class="sxs-lookup"><span data-stu-id="e0520-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e0520-401">Esta restricción se denomina *directiva del mismo origen.*</span><span class="sxs-lookup"><span data-stu-id="e0520-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e0520-402">La directiva del mismo origen impide que un sitio malintencionado lea datos confidenciales de otro sitio.</span><span class="sxs-lookup"><span data-stu-id="e0520-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e0520-403">A veces, es posible que desee permitir que otros sitios realicen solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="e0520-404">Para obtener más información, consulte el [artículo de Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="e0520-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e0520-405">Uso compartido de recursos entre [orígenes](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="e0520-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e0520-406">Es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e0520-407">**No** es una característica de seguridad, CORS relaja la seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0520-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e0520-408">Una API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e0520-409">Para obtener más información, consulte [Cómo funciona CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="e0520-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e0520-410">Permite que un servidor permita explícitamente algunas solicitudes entre orígenes mientras rechaza otras.</span><span class="sxs-lookup"><span data-stu-id="e0520-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e0520-411">Es más seguro y flexible que las técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="e0520-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e0520-412">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e0520-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e0520-413">Mismo origen</span><span class="sxs-lookup"><span data-stu-id="e0520-413">Same origin</span></span>

<span data-ttu-id="e0520-414">Dos direcciones URL tienen el mismo origen si tienen esquemas, hosts y puertos idénticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="e0520-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e0520-415">Estas dos direcciones URL tienen el mismo origen:</span><span class="sxs-lookup"><span data-stu-id="e0520-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e0520-416">Estas direcciones URL tienen orígenes diferentes a los dos URL anteriores:</span><span class="sxs-lookup"><span data-stu-id="e0520-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e0520-417">`https://example.net`&ndash; Dominio diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="e0520-418">`https://www.example.com/foo.html`&ndash; Subdominio diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="e0520-419">`http://example.com/foo.html`&ndash; Esquema diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="e0520-420">`https://example.com:9000/foo.html`&ndash; Puerto diferente</span><span class="sxs-lookup"><span data-stu-id="e0520-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="e0520-421">Internet Explorer no tiene en cuenta el puerto al comparar orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e0520-422">CORS con política y middleware con nombre</span><span class="sxs-lookup"><span data-stu-id="e0520-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="e0520-423">CORS Middleware controla las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e0520-424">El código siguiente habilita CORS para toda la aplicación con el origen especificado:</span><span class="sxs-lookup"><span data-stu-id="e0520-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="e0520-425">El código anterior:</span><span class="sxs-lookup"><span data-stu-id="e0520-425">The preceding code:</span></span>

* <span data-ttu-id="e0520-426">Establece el nombre\_de la directiva en " myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="e0520-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="e0520-427">El nombre de la directiva es arbitrario.</span><span class="sxs-lookup"><span data-stu-id="e0520-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e0520-428">Llama <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> al método de extensión, que habilita CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="e0520-429">Llamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con una [expresión lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="e0520-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e0520-430">La expresión <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> lambda toma un objeto.</span><span class="sxs-lookup"><span data-stu-id="e0520-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e0520-431">[Las opciones](#cors-policy-options)de `WithOrigins`configuración, como , se describen más adelante en este artículo.</span><span class="sxs-lookup"><span data-stu-id="e0520-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="e0520-432">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> llamada al método agrega servicios CORS al contenedor de servicios de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="e0520-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e0520-433">Para obtener más información, consulte Opciones de [directiva corS](#cpo) en este documento.</span><span class="sxs-lookup"><span data-stu-id="e0520-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="e0520-434">El <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método puede encadenar métodos, como se muestra en el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0520-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="e0520-435">Nota: La dirección URL **no** debe`/`contener una barra diagonal final ( ).</span><span class="sxs-lookup"><span data-stu-id="e0520-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e0520-436">Si la dirección `/`URL termina `false` con , se devuelve la comparación y no se devuelve ningún encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="e0520-437">El código siguiente aplica directivas CORS a todos los puntos de conexión de aplicaciones a través de CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="e0520-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="e0520-438">Nota: `UseCors` se debe `UseMvc`llamar antes de .</span><span class="sxs-lookup"><span data-stu-id="e0520-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="e0520-439">Consulte [Habilitar CORS en páginas de Razor, controladores y métodos](#ecors) de acción para aplicar la directiva CORS en el nivel de página/controlador/acción.</span><span class="sxs-lookup"><span data-stu-id="e0520-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="e0520-440">Consulte [Probar CORS](#test) para obtener instrucciones sobre cómo probar código similar al código anterior.</span><span class="sxs-lookup"><span data-stu-id="e0520-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e0520-441">Habilitar CORS con atributos</span><span class="sxs-lookup"><span data-stu-id="e0520-441">Enable CORS with attributes</span></span>

<span data-ttu-id="e0520-442">El atributo [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) proporciona una alternativa a la aplicación de CORS globalmente.</span><span class="sxs-lookup"><span data-stu-id="e0520-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e0520-443">El `[EnableCors]` atributo habilita CORS para los puntos finales seleccionados, en lugar de todos los puntos finales.</span><span class="sxs-lookup"><span data-stu-id="e0520-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="e0520-444">Se `[EnableCors]` utiliza para especificar `[EnableCors("{Policy String}")]` la directiva predeterminada y para especificar una directiva.</span><span class="sxs-lookup"><span data-stu-id="e0520-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="e0520-445">El `[EnableCors]` atributo se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="e0520-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="e0520-446">Página de Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="e0520-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="e0520-447">Controlador</span><span class="sxs-lookup"><span data-stu-id="e0520-447">Controller</span></span>
* <span data-ttu-id="e0520-448">Método de acción del controlador</span><span class="sxs-lookup"><span data-stu-id="e0520-448">Controller action method</span></span>

<span data-ttu-id="e0520-449">Puede aplicar diferentes directivas al controlador/modelo de `[EnableCors]` página/acción con el atributo.</span><span class="sxs-lookup"><span data-stu-id="e0520-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="e0520-450">Cuando `[EnableCors]` el atributo se aplica a un método de acción/modelo de página/controladores/páginas y CORS está habilitado en middleware, se aplican ***ambas*** directivas.</span><span class="sxs-lookup"><span data-stu-id="e0520-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="e0520-451">Se recomienda ***no*** combinar directivas.</span><span class="sxs-lookup"><span data-stu-id="e0520-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="e0520-452">Utilice `[EnableCors]` el atributo o middleware, \***no ambos**.</span><span class="sxs-lookup"><span data-stu-id="e0520-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="e0520-453">Cuando `[EnableCors]`utilice , **no** defina una directiva predeterminada.</span><span class="sxs-lookup"><span data-stu-id="e0520-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="e0520-454">El código siguiente aplica una directiva diferente a cada método:</span><span class="sxs-lookup"><span data-stu-id="e0520-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e0520-455">El código siguiente crea una directiva predeterminada `"AnotherPolicy"`de CORS y una directiva denominada:</span><span class="sxs-lookup"><span data-stu-id="e0520-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="e0520-456">Desactivar CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-456">Disable CORS</span></span>

<span data-ttu-id="e0520-457">El atributo [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deshabilita CORS para el controlador/modelo de página/acción.</span><span class="sxs-lookup"><span data-stu-id="e0520-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e0520-458">Opciones de política CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-458">CORS policy options</span></span>

<span data-ttu-id="e0520-459">Esta sección describe las diversas opciones que se pueden fijar en una directiva CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e0520-460">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e0520-461">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e0520-462">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e0520-463">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="e0520-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e0520-464">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="e0520-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e0520-465">Establecer el tiempo de expiración de la comprobación preliminar</span><span class="sxs-lookup"><span data-stu-id="e0520-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e0520-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>se llama `Startup.ConfigureServices`en .</span><span class="sxs-lookup"><span data-stu-id="e0520-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e0520-467">Para algunas opciones, puede ser útil leer primero la sección [Cómo funciona CORS.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="e0520-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e0520-468">Establecer los orígenes permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-468">Set the allowed origins</span></span>

<span data-ttu-id="e0520-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Permite solicitudes CORS de todos los`http` `https`orígenes con cualquier esquema ( o ).</span><span class="sxs-lookup"><span data-stu-id="e0520-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e0520-470">`AllowAnyOrigin`es inseguro porque *cualquier sitio web* puede hacer solicitudes entre orígenes a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e0520-471">Especificar `AllowAnyOrigin` y `AllowCredentials` es una configuración insegura y puede dar lugar a la falsificación de solicitudes entre sitios.</span><span class="sxs-lookup"><span data-stu-id="e0520-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e0520-472">Para una aplicación segura, especifique una lista exacta de orígenes si el cliente debe autorizarse a sí mismo para tener acceso a los recursos del servidor.</span><span class="sxs-lookup"><span data-stu-id="e0520-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="e0520-473">`AllowAnyOrigin`afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Origin` encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e0520-474">Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e0520-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e0520-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Establece <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> la propiedad de la directiva como una función que permite que los orígenes coincidan con un dominio comodín configurado al evaluar si se permite el origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e0520-476">Establecer los métodos HTTP permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e0520-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e0520-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e0520-478">Permite cualquier método HTTP:</span><span class="sxs-lookup"><span data-stu-id="e0520-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="e0520-479">Afecta a las solicitudes de comprobación previa y al `Access-Control-Allow-Methods` encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e0520-480">Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e0520-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e0520-481">Establecer los encabezados de solicitud permitidos</span><span class="sxs-lookup"><span data-stu-id="e0520-481">Set the allowed request headers</span></span>

<span data-ttu-id="e0520-482">Para permitir que se envíen encabezados específicos en una solicitud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> CORS, *denominadaencabezados*de solicitud de autor, llame y especifique los encabezados permitidos:</span><span class="sxs-lookup"><span data-stu-id="e0520-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e0520-483">Para permitir todos los encabezados de solicitud de autor, llame a: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="e0520-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e0520-484">Esta configuración afecta a las `Access-Control-Request-Headers` solicitudes de comprobación previa y al encabezado.</span><span class="sxs-lookup"><span data-stu-id="e0520-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="e0520-485">Para obtener más información, consulte la sección Solicitudes de [comprobación preliminar.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e0520-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e0520-486">CORS Middleware siempre permite que `Access-Control-Request-Headers` se envíen cuatro encabezados en el que se enviarán independientemente de los valores configurados en CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="e0520-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="e0520-487">Esta lista de encabezados incluye:</span><span class="sxs-lookup"><span data-stu-id="e0520-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="e0520-488">Por ejemplo, considere una aplicación configurada de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="e0520-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e0520-489">CorS Middleware responde correctamente a una solicitud de comprobación `Content-Language` previa con el siguiente encabezado de solicitud porque siempre está en la lista blanca:</span><span class="sxs-lookup"><span data-stu-id="e0520-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e0520-490">Establecer los encabezados de respuesta expuestos</span><span class="sxs-lookup"><span data-stu-id="e0520-490">Set the exposed response headers</span></span>

<span data-ttu-id="e0520-491">De forma predeterminada, el explorador no expone todos los encabezados de respuesta a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e0520-492">Para obtener más información, consulte Uso compartido de recursos entre orígenes [(terminología): Encabezado](https://www.w3.org/TR/cors/#simple-response-header)de respuesta simple .</span><span class="sxs-lookup"><span data-stu-id="e0520-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e0520-493">Los encabezados de respuesta que están disponibles de forma predeterminada son:</span><span class="sxs-lookup"><span data-stu-id="e0520-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e0520-494">La especificación CORS llama a estos *encabezados de respuesta simples.*</span><span class="sxs-lookup"><span data-stu-id="e0520-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e0520-495">Para que otros encabezados estén <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>disponibles para la aplicación, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e0520-496">Credenciales en solicitudes entre orígenes</span><span class="sxs-lookup"><span data-stu-id="e0520-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e0520-497">Las credenciales requieren un control especial en una solicitud CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e0520-498">De forma predeterminada, el explorador no envía credenciales con una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e0520-499">Las credenciales incluyen cookies y esquemas de autenticación HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0520-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e0520-500">Para enviar credenciales con una solicitud entre `XMLHttpRequest.withCredentials` orígenes, el cliente debe establecerse en `true`.</span><span class="sxs-lookup"><span data-stu-id="e0520-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e0520-501">Usando `XMLHttpRequest` directamente:</span><span class="sxs-lookup"><span data-stu-id="e0520-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e0520-502">Uso de jQuery:</span><span class="sxs-lookup"><span data-stu-id="e0520-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e0520-503">Uso de la [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="e0520-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e0520-504">El servidor debe permitir las credenciales.</span><span class="sxs-lookup"><span data-stu-id="e0520-504">The server must allow the credentials.</span></span> <span data-ttu-id="e0520-505">Para permitir credenciales entre <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>orígenes, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="e0520-506">La respuesta HTTP `Access-Control-Allow-Credentials` incluye un encabezado, que indica al explorador que el servidor permite credenciales para una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e0520-507">Si el explorador envía credenciales pero la `Access-Control-Allow-Credentials` respuesta no incluye un encabezado válido, el explorador no expone la respuesta a la aplicación y se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e0520-508">Permitir credenciales entre orígenes es un riesgo para la seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0520-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e0520-509">Un sitio web de otro dominio puede enviar las credenciales de un usuario que ha iniciado sesión a la aplicación en nombre del usuario sin el conocimiento del usuario.</span><span class="sxs-lookup"><span data-stu-id="e0520-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e0520-510">La especificación CORS también indica `"*"` que establecer los orígenes `Access-Control-Allow-Credentials` en (todos los orígenes) no es válido si el encabezado está presente.</span><span class="sxs-lookup"><span data-stu-id="e0520-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="e0520-511">Solicitudes de comprobación previa</span><span class="sxs-lookup"><span data-stu-id="e0520-511">Preflight requests</span></span>

<span data-ttu-id="e0520-512">Para algunas solicitudes CORS, el explorador envía una solicitud adicional antes de realizar la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="e0520-513">Esta solicitud se denomina solicitud de *comprobación previa.*</span><span class="sxs-lookup"><span data-stu-id="e0520-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="e0520-514">El navegador puede omitir la solicitud de comprobación previa si se cumplen las siguientes condiciones:</span><span class="sxs-lookup"><span data-stu-id="e0520-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="e0520-515">El método de solicitud es GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="e0520-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e0520-516">La aplicación no establece encabezados `Accept`de `Accept-Language` `Content-Language`solicitud `Content-Type`que `Last-Event-ID`no sean , , , o .</span><span class="sxs-lookup"><span data-stu-id="e0520-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e0520-517">El `Content-Type` encabezado, si se establece, tiene uno de los siguientes valores:</span><span class="sxs-lookup"><span data-stu-id="e0520-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e0520-518">La regla en los encabezados de solicitud establecidos para la `setRequestHeader` solicitud `XMLHttpRequest` de cliente se aplica a los encabezados que establece la aplicación mediante una llamada en el objeto.</span><span class="sxs-lookup"><span data-stu-id="e0520-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e0520-519">La especificación CORS llama a estos encabezados de solicitud de autor de *encabezados*.</span><span class="sxs-lookup"><span data-stu-id="e0520-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="e0520-520">La regla no se aplica a los encabezados `User-Agent` `Host`que `Content-Length`el explorador puede establecer, como , , o .</span><span class="sxs-lookup"><span data-stu-id="e0520-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e0520-521">A continuación se muestra un ejemplo de una solicitud de comprobación previa:</span><span class="sxs-lookup"><span data-stu-id="e0520-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="e0520-522">La solicitud de pre-vuelo utiliza el método HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="e0520-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="e0520-523">Incluye dos encabezados especiales:</span><span class="sxs-lookup"><span data-stu-id="e0520-523">It includes two special headers:</span></span>

* <span data-ttu-id="e0520-524">`Access-Control-Request-Method`: el método HTTP que se utilizará para la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e0520-525">`Access-Control-Request-Headers`: una lista de encabezados de solicitud que la aplicación establece en la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e0520-526">Como se indicó anteriormente, esto no incluye encabezados `User-Agent`que el explorador establece, como .</span><span class="sxs-lookup"><span data-stu-id="e0520-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="e0520-527">Cuando CORS está habilitado con la directiva adecuada, ASP.NET Core generalmente responde automáticamente a las solicitudes de comprobación previa de CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="e0520-528">Consulte el [atributo [HttpOptions] para ver las solicitudes](#pro)de comprobación previa.</span><span class="sxs-lookup"><span data-stu-id="e0520-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="e0520-529">Una solicitud de comprobación previa `Access-Control-Request-Headers` de CORS puede incluir un encabezado, que indica al servidor los encabezados que se envían con la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="e0520-530">Para permitir encabezados <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>específicos, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e0520-531">Para permitir todos los encabezados de solicitud de autor, llame a: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="e0520-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e0520-532">Los navegadores no son del `Access-Control-Request-Headers`todo coherentes en la forma en que establecen .</span><span class="sxs-lookup"><span data-stu-id="e0520-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e0520-533">Si establece encabezados en `"*"` cualquier otro <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>valor que no `Accept` `Content-Type`sea `Origin`(o use ), debe incluir al menos , , y , además de los encabezados personalizados que desee admitir.</span><span class="sxs-lookup"><span data-stu-id="e0520-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="e0520-534">A continuación se muestra una respuesta de ejemplo a la solicitud de comprobación previa (suponiendo que el servidor permite la solicitud):</span><span class="sxs-lookup"><span data-stu-id="e0520-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="e0520-535">La respuesta `Access-Control-Allow-Methods` incluye un encabezado que enumera `Access-Control-Allow-Headers` los métodos permitidos y, opcionalmente, un encabezado, que enumera los encabezados permitidos.</span><span class="sxs-lookup"><span data-stu-id="e0520-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="e0520-536">Si la solicitud de comprobación previa se realiza correctamente, el explorador envía la solicitud real.</span><span class="sxs-lookup"><span data-stu-id="e0520-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="e0520-537">Si se deniega la solicitud de comprobación previa, la aplicación devuelve una respuesta *200 OK* pero no devuelve los encabezados CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e0520-538">Por lo tanto, el explorador no intenta la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e0520-539">Establecer el tiempo de expiración de la comprobación preliminar</span><span class="sxs-lookup"><span data-stu-id="e0520-539">Set the preflight expiration time</span></span>

<span data-ttu-id="e0520-540">El `Access-Control-Max-Age` encabezado especifica cuánto tiempo se puede almacenar en caché la respuesta a la solicitud de comprobación previa.</span><span class="sxs-lookup"><span data-stu-id="e0520-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e0520-541">Para establecer este <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>encabezado, llame a:</span><span class="sxs-lookup"><span data-stu-id="e0520-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e0520-542">Cómo funciona CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-542">How CORS works</span></span>

<span data-ttu-id="e0520-543">En esta sección se describe lo que sucede en una solicitud [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) en el nivel de los mensajes HTTP.</span><span class="sxs-lookup"><span data-stu-id="e0520-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e0520-544">CORS **no** es una característica de seguridad.</span><span class="sxs-lookup"><span data-stu-id="e0520-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="e0520-545">CORS es un estándar W3C que permite a un servidor relajar la directiva del mismo origen.</span><span class="sxs-lookup"><span data-stu-id="e0520-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e0520-546">Por ejemplo, un actor malintencionado podría usar [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) en su sitio y ejecutar una solicitud entre sitios a su sitio habilitado para CORS para robar información.</span><span class="sxs-lookup"><span data-stu-id="e0520-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e0520-547">Su API no es más segura al permitir CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="e0520-548">Depende del cliente (navegador) aplicar CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e0520-549">El servidor ejecuta la solicitud y devuelve la respuesta, es el cliente el que devuelve un error y bloquea la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e0520-550">Por ejemplo, cualquiera de las siguientes herramientas mostrará la respuesta del servidor:</span><span class="sxs-lookup"><span data-stu-id="e0520-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e0520-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e0520-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e0520-552">Postman</span><span class="sxs-lookup"><span data-stu-id="e0520-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e0520-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="e0520-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e0520-554">Un explorador web introduciendo la dirección URL en la barra de direcciones.</span><span class="sxs-lookup"><span data-stu-id="e0520-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e0520-555">Es una forma de que un servidor permita a los navegadores ejecutar una solicitud de API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) o [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) de origen cruzado que, de lo contrario, estaría prohibida.</span><span class="sxs-lookup"><span data-stu-id="e0520-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e0520-556">Los navegadores (sin CORS) no pueden realizar solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="e0520-557">Antes de CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) se usaba para eludir esta restricción.</span><span class="sxs-lookup"><span data-stu-id="e0520-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e0520-558">JSONP no usa XHR, usa `<script>` la etiqueta para recibir la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e0520-559">Los scripts se pueden cargar entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e0520-560">La [especificación CORS](https://www.w3.org/TR/cors/) introdujo varios encabezados HTTP nuevos que habilitan las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e0520-561">Si un explorador admite CORS, establece estos encabezados automáticamente para las solicitudes entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e0520-562">El código JavaScript personalizado no es necesario para habilitar CORS.</span><span class="sxs-lookup"><span data-stu-id="e0520-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e0520-563">A continuación se muestra un ejemplo de una solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="e0520-564">El `Origin` encabezado proporciona el dominio del sitio que realiza la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="e0520-565">El `Origin` encabezado es obligatorio y debe ser diferente del host.</span><span class="sxs-lookup"><span data-stu-id="e0520-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="e0520-566">Si el servidor permite la `Access-Control-Allow-Origin` solicitud, establece el encabezado en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="e0520-567">El valor de este `Origin` encabezado coincide con el encabezado `"*"`de la solicitud o es el valor comodín, lo que significa que se permite cualquier origen:</span><span class="sxs-lookup"><span data-stu-id="e0520-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="e0520-568">Si la respuesta no `Access-Control-Allow-Origin` incluye el encabezado, se produce un error en la solicitud entre orígenes.</span><span class="sxs-lookup"><span data-stu-id="e0520-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e0520-569">Específicamente, el navegador no permite la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e0520-570">Incluso si el servidor devuelve una respuesta correcta, el explorador no hace que la respuesta esté disponible para la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="e0520-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="e0520-571">Prueba de CORS</span><span class="sxs-lookup"><span data-stu-id="e0520-571">Test CORS</span></span>

<span data-ttu-id="e0520-572">Para probar CORS:</span><span class="sxs-lookup"><span data-stu-id="e0520-572">To test CORS:</span></span>

1. <span data-ttu-id="e0520-573">[Cree un proyecto](xref:tutorials/first-web-api)de API.</span><span class="sxs-lookup"><span data-stu-id="e0520-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="e0520-574">Como alternativa, puede [descargar el ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="e0520-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="e0520-575">Habilite CORS utilizando uno de los enfoques de este documento.</span><span class="sxs-lookup"><span data-stu-id="e0520-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="e0520-576">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e0520-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="e0520-577">`WithOrigins("https://localhost:<port>");`solo se debe usar para probar una aplicación de ejemplo similar al código de ejemplo de [descarga.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)</span><span class="sxs-lookup"><span data-stu-id="e0520-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="e0520-578">Crear un proyecto de aplicación web (Razor Pages o MVC).</span><span class="sxs-lookup"><span data-stu-id="e0520-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="e0520-579">El ejemplo utiliza Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e0520-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="e0520-580">Puede crear la aplicación web en la misma solución que el proyecto de API.</span><span class="sxs-lookup"><span data-stu-id="e0520-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="e0520-581">Agregue el siguiente código resaltado al archivo *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="e0520-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="e0520-582">En el código anterior, reemplace `url: 'https://<web app>.azurewebsites.net/api/values/1',` por la dirección URL de la aplicación implementada.</span><span class="sxs-lookup"><span data-stu-id="e0520-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="e0520-583">Implemente el proyecto de API.</span><span class="sxs-lookup"><span data-stu-id="e0520-583">Deploy the API project.</span></span> <span data-ttu-id="e0520-584">Por ejemplo, [implemente en Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="e0520-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="e0520-585">Ejecute la aplicación Razor Pages o MVC desde el escritorio y haga clic en el botón **Probar.**</span><span class="sxs-lookup"><span data-stu-id="e0520-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="e0520-586">Utilice las herramientas F12 para revisar los mensajes de error.</span><span class="sxs-lookup"><span data-stu-id="e0520-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="e0520-587">Quite el origen `WithOrigins` localhost de la aplicación e implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="e0520-588">Como alternativa, ejecute la aplicación cliente con un puerto diferente.</span><span class="sxs-lookup"><span data-stu-id="e0520-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="e0520-589">Por ejemplo, ejecute desde Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e0520-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="e0520-590">Pruebe con la aplicación cliente.</span><span class="sxs-lookup"><span data-stu-id="e0520-590">Test with the client app.</span></span> <span data-ttu-id="e0520-591">Los errores de CORS devuelven un error, pero el mensaje de error no está disponible para JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e0520-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="e0520-592">Utilice la pestaña de la consola de las herramientas F12 para ver el error.</span><span class="sxs-lookup"><span data-stu-id="e0520-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="e0520-593">Dependiendo del navegador, aparece un error (en la consola de herramientas F12) similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="e0520-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="e0520-594">Uso de Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="e0520-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="e0520-595">**SEC7120: [CORS] `https://localhost:44375` El origen `https://localhost:44375` no se encontró en el encabezado de respuesta Access-Control-Allow-Origin para el recurso entre orígenes en`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="e0520-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="e0520-596">Uso de Chrome:</span><span class="sxs-lookup"><span data-stu-id="e0520-596">Using Chrome:</span></span>

     <span data-ttu-id="e0520-597">**El acceso a `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest desde el origen `https://localhost:44375` se ha bloqueado mediante la directiva CORS: No hay ningún encabezado 'Access-Control-Allow-Origin' en el recurso solicitado.**</span><span class="sxs-lookup"><span data-stu-id="e0520-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="e0520-598">Los puntos finales habilitados para CORS se pueden probar con una herramienta, como [Fiddler](https://www.telerik.com/fiddler) o [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="e0520-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e0520-599">Cuando se utiliza una herramienta, el origen `Origin` de la solicitud especificada por el encabezado debe diferir del host que recibe la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e0520-600">Si la solicitud no es *de origen* cruzado `Origin` en función del valor del encabezado:</span><span class="sxs-lookup"><span data-stu-id="e0520-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e0520-601">No es necesario que CORS Middleware procese la solicitud.</span><span class="sxs-lookup"><span data-stu-id="e0520-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e0520-602">Los encabezados CORS no se devuelven en la respuesta.</span><span class="sxs-lookup"><span data-stu-id="e0520-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="e0520-603">CORS en IIS</span><span class="sxs-lookup"><span data-stu-id="e0520-603">CORS in IIS</span></span>

<span data-ttu-id="e0520-604">Al implementar en IIS, CORS tiene que ejecutarse antes de la autenticación de Windows si el servidor no está configurado para permitir el acceso anónimo.</span><span class="sxs-lookup"><span data-stu-id="e0520-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="e0520-605">Para admitir este escenario, el [módulo CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) de IIS debe instalarse y configurarse para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e0520-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0520-606">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="e0520-606">Additional resources</span></span>

* [<span data-ttu-id="e0520-607">Uso compartido de recursos entre orígenes (CORS)</span><span class="sxs-lookup"><span data-stu-id="e0520-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="e0520-608">Introducción al módulo CorS de IIS</span><span class="sxs-lookup"><span data-stu-id="e0520-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
