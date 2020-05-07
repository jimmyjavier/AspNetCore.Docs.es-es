---
title: Configuración de ASP.NET Core para trabajar con servidores proxy y equilibradores de carga
author: rick-anderson
description: Aprenda sobre la configuración de las aplicaciones hospedadas detrás de los servidores proxy y equilibradores de carga, que con frecuencia ocultan información importante sobre las solicitudes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e329b8604b820818167a563b3a21f01f2ab2a6ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776388"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="b9a60-103">Configuración de ASP.NET Core para trabajar con servidores proxy y equilibradores de carga</span><span class="sxs-lookup"><span data-stu-id="b9a60-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="b9a60-104">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b9a60-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b9a60-105">En la configuración recomendada de ASP.NET Core, la aplicación se hospeda mediante IIS/módulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="b9a60-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="b9a60-106">Los servidores proxy, los equilibradores de carga y otros dispositivos de red con frecuencia ocultan información sobre la solicitud antes de que llegue a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b9a60-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="b9a60-107">Cuando las solicitudes HTTPS se redirigen mediante proxy a través de HTTP, el esquema original (HTTPS) se pierde y se debe reenviar en un encabezado.</span><span class="sxs-lookup"><span data-stu-id="b9a60-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="b9a60-108">Como una aplicación recibe una solicitud del proxy y no desde su verdadero origen en Internet o la red corporativa, la dirección IP del cliente de origen también se debe reenviar en el encabezado.</span><span class="sxs-lookup"><span data-stu-id="b9a60-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="b9a60-109">Esta información puede ser importante en el procesamiento de las solicitudes, por ejemplo, en los redireccionamientos, la autenticación, la generación de vínculos, la evaluación de directivas y la geolocalización del cliente.</span><span class="sxs-lookup"><span data-stu-id="b9a60-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="b9a60-110">Encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="b9a60-110">Forwarded headers</span></span>

<span data-ttu-id="b9a60-111">Por costumbre, los servidores proxy reenvían la información en encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9a60-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="b9a60-112">Header</span><span class="sxs-lookup"><span data-stu-id="b9a60-112">Header</span></span> | <span data-ttu-id="b9a60-113">Descripción</span><span class="sxs-lookup"><span data-stu-id="b9a60-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="b9a60-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="b9a60-114">X-Forwarded-For</span></span> | <span data-ttu-id="b9a60-115">Contiene información sobre el cliente que inició la solicitud y los servidores proxy posteriores en una cadena de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="b9a60-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="b9a60-116">Este parámetro puede contener direcciones IP (y, opcionalmente, números de puerto).</span><span class="sxs-lookup"><span data-stu-id="b9a60-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="b9a60-117">En una cadena de servidores proxy, el primer parámetro indica al cliente dónde se realizó primero la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b9a60-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="b9a60-118">Le siguen los identificadores de proxy posteriores.</span><span class="sxs-lookup"><span data-stu-id="b9a60-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="b9a60-119">El último proxy en la cadena no se encuentra en la lista de parámetros.</span><span class="sxs-lookup"><span data-stu-id="b9a60-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="b9a60-120">La última dirección IP del proxy y, opcionalmente, un número de puerto, está disponible como la dirección IP remota en la capa de transporte.</span><span class="sxs-lookup"><span data-stu-id="b9a60-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="b9a60-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="b9a60-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="b9a60-122">El valor del esquema de origen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b9a60-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="b9a60-123">El valor también puede ser una lista de esquemas si la solicitud ha pasado por varios servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="b9a60-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="b9a60-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="b9a60-124">X-Forwarded-Host</span></span> | <span data-ttu-id="b9a60-125">El valor original del campo de encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="b9a60-125">The original value of the Host header field.</span></span> <span data-ttu-id="b9a60-126">Por lo general, los servidores proxy no modifican el encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="b9a60-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="b9a60-127">Consulte [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para información sobre una vulnerabilidad de elevación de privilegios que afecta a sistemas donde el proxy no valida ni restringe los encabezados de host a valores buenos conocidos.</span><span class="sxs-lookup"><span data-stu-id="b9a60-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="b9a60-128">El Middleware de encabezados reenviados, del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lee estos encabezados y rellena los campos asociados en <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="b9a60-129">El middleware realiza las siguientes actualizaciones:</span><span class="sxs-lookup"><span data-stu-id="b9a60-129">The middleware updates:</span></span>

* <span data-ttu-id="b9a60-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): establézcalo mediante el valor de encabezado `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="b9a60-131">Los valores de configuración adicionales afectan a cómo el middleware establece `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="b9a60-132">Para más información, consulte la sección [Opciones de Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="b9a60-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="b9a60-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): establézcalo mediante el valor de encabezado `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="b9a60-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): establézcalo mediante el valor de encabezado `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="b9a60-135">Se pueden configurar los [valores predeterminados](#forwarded-headers-middleware-options) del Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="b9a60-136">Estos valores son:</span><span class="sxs-lookup"><span data-stu-id="b9a60-136">The default settings are:</span></span>

* <span data-ttu-id="b9a60-137">Solo hay *un proxy* entre la aplicación y el origen de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b9a60-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="b9a60-138">Solo las direcciones de bucle invertido se configuran para servidores proxy conocidos y redes conocidas.</span><span class="sxs-lookup"><span data-stu-id="b9a60-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="b9a60-139">Los encabezados reenviados se denominan `X-Forwarded-For` y `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="b9a60-140">No todos los dispositivos de red agregan los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="b9a60-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="b9a60-141">Consulte las instrucciones del fabricante de su dispositivo si las solicitudes redirigidas mediante proxy no contienen estos encabezados cuando llegan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9a60-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="b9a60-142">Si el dispositivo usa nombres de encabezado distintos a `X-Forwarded-For` y `X-Forwarded-Proto`, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para que coincidan con los nombres de encabezado empleados por el dispositivo.</span><span class="sxs-lookup"><span data-stu-id="b9a60-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="b9a60-143">Para obtener más información, vea [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options) y [Configuración de un proxy que usa otros nombres de encabezado](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="b9a60-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="b9a60-144">IIS o IIS Express y el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9a60-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="b9a60-145">El Middleware de encabezados reenviados se habilita de forma predeterminada mediante el [Middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) cuando la aplicación se hospeda [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás de IIS y del módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9a60-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="b9a60-146">El Middleware de encabezados reenviados está activado para ejecutarse primero en la canalización de middleware con una configuración restringida específica del módulo ASP.NET Core debido a problemas de confianza con los encabezados reenviados (por ejemplo, [suplantación de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="b9a60-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="b9a60-147">El middleware está configurado para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` y está restringido a un único proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="b9a60-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="b9a60-148">Si se requiere configuración adicional, consulte la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="b9a60-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9a60-149">Otros escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="b9a60-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9a60-150">Al margen del uso de la [integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), el Middleware de encabezados reenviados no está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b9a60-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="b9a60-151">El middleware de encabezados reenviados debe estar habilitado en una aplicación para procesar los encabezados reenviados con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="b9a60-152">Después de habilitar el middleware, si no se especifica <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para él, el valor predeterminado [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="b9a60-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="b9a60-153">Configure el middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b9a60-154">Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure` antes de llamar a otro middleware:</span><span class="sxs-lookup"><span data-stu-id="b9a60-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="b9a60-155">Si no se especifica ningún <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> en `Startup.ConfigureServices` o directamente en el método de extensión con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, los encabezados predeterminados para reenviar son [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="b9a60-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="b9a60-156">La propiedad <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> debe estar configurada con los encabezados que se van a reenviar.</span><span class="sxs-lookup"><span data-stu-id="b9a60-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="b9a60-157">Configuración de Nginx</span><span class="sxs-lookup"><span data-stu-id="b9a60-157">Nginx configuration</span></span>

<span data-ttu-id="b9a60-158">Para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="b9a60-159">Para obtener más información, consulte [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: uso del encabezado Forwarded).</span><span class="sxs-lookup"><span data-stu-id="b9a60-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="b9a60-160">Configuración de Apache</span><span class="sxs-lookup"><span data-stu-id="b9a60-160">Apache configuration</span></span>

<span data-ttu-id="b9a60-161">`X-Forwarded-For` se agrega automáticamente. Consulte [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Módulo de Apache mod_proxy: Encabezados de solicitud de proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="b9a60-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="b9a60-162">Para obtener información sobre cómo reenviar el encabezado `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="b9a60-163">Opciones del Middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="b9a60-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="b9a60-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla el comportamiento del middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="b9a60-165">En el ejemplo siguiente se cambian los valores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="b9a60-165">The following example changes the default values:</span></span>

* <span data-ttu-id="b9a60-166">Limite el número de entradas de los encabezados reenviados a `2`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="b9a60-167">Agregue una dirección de proxy conocida de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="b9a60-168">Cambie el nombre del encabezado reenviado del valor predeterminado `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="b9a60-169">Opción</span><span class="sxs-lookup"><span data-stu-id="b9a60-169">Option</span></span> | <span data-ttu-id="b9a60-170">Descripción</span><span class="sxs-lookup"><span data-stu-id="b9a60-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="b9a60-171">Restringe los hosts por el encabezado `X-Forwarded-Host` a los valores proporcionados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="b9a60-172">Los valores se comparan mediante ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="b9a60-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="b9a60-173">Se deben excluir los números de puerto.</span><span class="sxs-lookup"><span data-stu-id="b9a60-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="b9a60-174">Si la lista está vacía, se permiten todos los hosts.</span><span class="sxs-lookup"><span data-stu-id="b9a60-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="b9a60-175">Un carácter comodín de nivel superior `*` permite que todos los hosts que no están vacíos.</span><span class="sxs-lookup"><span data-stu-id="b9a60-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="b9a60-176">Se permiten caracteres comodín de subdominio, pero no coinciden con el dominio raíz.</span><span class="sxs-lookup"><span data-stu-id="b9a60-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="b9a60-177">Por ejemplo, `*.contoso.com` coincide con el subdominio `foo.contoso.com` pero no con el dominio raíz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="b9a60-178">Se permiten nombres de host Unicode, pero se convierten en [Punycode](https://tools.ietf.org/html/rfc3492) para buscar la coincidencia.</span><span class="sxs-lookup"><span data-stu-id="b9a60-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="b9a60-179">Las [direcciones IPv6](https://tools.ietf.org/html/rfc4291) deben incluir corchetes de enlace y estar en [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por ejemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="b9a60-180">Las direcciones IPv6 no usan mayúsculas y minúsculas de forma especial para buscar la igualdad lógica entre diferentes formatos, y no se realiza ninguna canonización.</span><span class="sxs-lookup"><span data-stu-id="b9a60-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="b9a60-181">Si no se restringen los hosts permitidos, un atacante podría suplantar los vínculos generados por el servicio.</span><span class="sxs-lookup"><span data-stu-id="b9a60-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="b9a60-182">El valor predeterminado es un `IList<string>` vacío.</span><span class="sxs-lookup"><span data-stu-id="b9a60-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="b9a60-183">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="b9a60-184">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-For` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="b9a60-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b9a60-185">De manera predeterminada, es `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="b9a60-186">Identifica qué reenviadores se deben procesar.</span><span class="sxs-lookup"><span data-stu-id="b9a60-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="b9a60-187">Consulte [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para obtener la lista de campos que se aplican.</span><span class="sxs-lookup"><span data-stu-id="b9a60-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="b9a60-188">Los valores típicos que se asignan a esta propiedad son `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="b9a60-189">El valor predeterminado es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="b9a60-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="b9a60-190">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="b9a60-191">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Host` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="b9a60-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b9a60-192">De manera predeterminada, es `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="b9a60-193">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="b9a60-194">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Proto` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="b9a60-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b9a60-195">De manera predeterminada, es `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="b9a60-196">Limita el número de entradas en los encabezados que se procesan.</span><span class="sxs-lookup"><span data-stu-id="b9a60-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="b9a60-197">Establézcalo en `null` para deshabilitar el límite, pero esto solo se debe realizar si están configurados `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="b9a60-198">Establecerlo en un valor que no sea `null` es una medida de precaución (no una garantía) para protegerse contra los proxies mal configurados y las peticiones maliciosas que llegan desde los canales laterales de la red.</span><span class="sxs-lookup"><span data-stu-id="b9a60-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="b9a60-199">El middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="b9a60-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b9a60-200">Si se usa el valor predeterminado (`1`), solo se procesa el valor más a la derecha de los encabezados, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="b9a60-201">De manera predeterminada, es `1`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="b9a60-202">Intervalos de direcciones de redes conocidas de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="b9a60-203">Proporcione intervalos de direcciones IP mediante la notación de Enrutamiento de interdominios sin clases (CIDR).</span><span class="sxs-lookup"><span data-stu-id="b9a60-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="b9a60-204">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b9a60-205">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b9a60-206">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b9a60-207">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="b9a60-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b9a60-208">El valor predeterminado es un `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contiene una única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="b9a60-209">Direcciones de servidores proxy conocidos de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="b9a60-210">Use `KnownProxies` para especificar las coincidencias exactas de direcciones IP.</span><span class="sxs-lookup"><span data-stu-id="b9a60-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="b9a60-211">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b9a60-212">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b9a60-213">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b9a60-214">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="b9a60-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b9a60-215">El valor predeterminado es un `IList`\<<xref:System.Net.IPAddress>> que contiene una única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="b9a60-216">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="b9a60-217">De manera predeterminada, es `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="b9a60-218">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="b9a60-219">De manera predeterminada, es `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="b9a60-220">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="b9a60-221">De manera predeterminada, es `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="b9a60-222">Requiere que el número de valores de encabezado esté sincronizado entre los valores [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) que se van a procesar.</span><span class="sxs-lookup"><span data-stu-id="b9a60-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="b9a60-223">El valor predeterminado en ASP.NET Core 1.x es `true`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="b9a60-224">El valor predeterminado en ASP.NET Core 2.0 o posterior es `false`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="b9a60-225">Escenarios y casos de uso</span><span class="sxs-lookup"><span data-stu-id="b9a60-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="b9a60-226">Cuando no es posible agregar encabezados reenviados y todas las solicitudes son seguras</span><span class="sxs-lookup"><span data-stu-id="b9a60-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="b9a60-227">En algunos casos, puede que no sea posible agregar encabezados reenviados a las solicitudes redirigidas mediante proxy a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9a60-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="b9a60-228">Si el proxy está forzando a que todas las solicitudes externas públicas sean HTTPS, el esquema se puede establecer manualmente en `Startup.Configure` antes de usar cualquier tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="b9a60-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="b9a60-229">Este código puede deshabilitarse con una variable de entorno u otro valor de configuración en un entorno de desarrollo o ensayo.</span><span class="sxs-lookup"><span data-stu-id="b9a60-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="b9a60-230">Tratar con la ruta de acceso base y los servidores proxy que cambian la ruta de acceso de la solicitud</span><span class="sxs-lookup"><span data-stu-id="b9a60-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="b9a60-231">Algunos servidores proxy pasan la ruta de acceso sin cambios pero con una ruta de acceso base de aplicación que se debe quitar para que el enrutamiento funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="b9a60-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="b9a60-232">El middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide la ruta de acceso en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) y la ruta de acceso base de aplicación en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="b9a60-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="b9a60-233">Si `/foo` es la ruta de acceso base de aplicación para una ruta de acceso de proxy que se pasa como `/foo/api/1`, el middleware establece `Request.PathBase` en `/foo` y `Request.Path` en `/api/1` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b9a60-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="b9a60-234">La ruta de acceso base y la ruta de acceso original se vuelven a aplicar cuando se llama de nuevo al middleware en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="b9a60-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="b9a60-235">Para obtener más información sobre el procesamiento de pedidos del middleware, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="b9a60-236">Si el proxy recorta la ruta de acceso (por ejemplo, el reenvío `/foo/api/1` a `/api/1`), corrija los redireccionamientos y los vínculos mediante el establecimiento de la propiedad [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="b9a60-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="b9a60-237">Si el proxy va a agregar datos de ruta de acceso, descarte parte de esta ruta para corregir los redireccionamientos y los vínculos; para ello, use <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> y asígnelo a la propiedad <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="b9a60-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="b9a60-238">Configuración de un proxy que usa otros nombres de encabezado</span><span class="sxs-lookup"><span data-stu-id="b9a60-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="b9a60-239">Si el proxy no usa los encabezados denominados `X-Forwarded-For` y `X-Forwarded-Proto` para reenviar el puerto o la dirección de proxy y originar información de esquema, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> de modo que coincidan con los nombres de encabezado empleados por el proxy:</span><span class="sxs-lookup"><span data-stu-id="b9a60-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="b9a60-240">Configuración de una dirección IPv4 representada como una dirección IPv6</span><span class="sxs-lookup"><span data-stu-id="b9a60-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="b9a60-241">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="b9a60-242">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b9a60-243">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="b9a60-244">En el ejemplo siguiente, se agrega una dirección de red que proporciona encabezados reenviados a la lista `KnownNetworks` en formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="b9a60-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="b9a60-245">Dirección IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="b9a60-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="b9a60-246">Dirección IPv6 convertida: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="b9a60-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="b9a60-247">Longitud de prefijo convertida: 104</span><span class="sxs-lookup"><span data-stu-id="b9a60-247">Converted prefix length: 104</span></span>

<span data-ttu-id="b9a60-248">También puede proporcionar la dirección en formato hexadecimal (`10.11.12.1` se representa en IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="b9a60-249">Al convertir una dirección IPv4 en IPv6, agregue 96 a la longitud de prefijo de CIDR (`8` en el ejemplo) para tener en cuenta el prefijo de IPv6 `::ffff:` adicional (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="b9a60-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="b9a60-250">Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS</span><span class="sxs-lookup"><span data-stu-id="b9a60-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="b9a60-251">Las aplicaciones que llaman a los métodos <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> y <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> incluyen un sitio en un bucle infinito si se implementan en una instancia de Azure App Service de Linux, una máquina virtual Linux de Azure, o bien detrás de cualquier otro servidor proxy inverso, además de IIS.</span><span class="sxs-lookup"><span data-stu-id="b9a60-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="b9a60-252">El servidor proxy inverso termina TLS y Kestrel no es consciente del esquema de solicitud correcto.</span><span class="sxs-lookup"><span data-stu-id="b9a60-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="b9a60-253">En esta configuración también se produce un error de OAuth y OIDC, ya que generan redirecciones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="b9a60-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="b9a60-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> agrega y configura middleware de encabezados reenviados cuando se ejecuta detrás de IIS, pero no hay ninguna configuración automática coincidente para Linux (integración de Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="b9a60-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="b9a60-255">Para reenviar el esquema desde el servidor proxy en escenarios que no sean de IIS, agregue y configure Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="b9a60-256">En `Startup.ConfigureServices`, use el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b9a60-256">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="b9a60-257">Reenvío de certificados</span><span class="sxs-lookup"><span data-stu-id="b9a60-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="b9a60-258">Azure</span><span class="sxs-lookup"><span data-stu-id="b9a60-258">Azure</span></span>

<span data-ttu-id="b9a60-259">Para configurar Azure App Service para el reenvío de certificados, consulte [Configuración de la autenticación mutua de TLS en Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="b9a60-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="b9a60-260">La guía siguiente se aplica a la configuración de la aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9a60-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="b9a60-261">En `Startup.Configure`, agregue el código siguiente antes de llamar a `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="b9a60-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="b9a60-262">Configure el middleware de reenvío de certificados para especificar el nombre de encabezado que Azure usa.</span><span class="sxs-lookup"><span data-stu-id="b9a60-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="b9a60-263">En `Startup.ConfigureServices`, agregue el código siguiente para configurar el encabezado desde el que el middleware crea un certificado:</span><span class="sxs-lookup"><span data-stu-id="b9a60-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="b9a60-264">Otros servidores proxy web</span><span class="sxs-lookup"><span data-stu-id="b9a60-264">Other web proxies</span></span>

<span data-ttu-id="b9a60-265">Si se usa un proxy que no es IIS ni Enrutamiento de solicitud de aplicaciones de Azure App Service, configure el proxy para reenviar el certificado que recibió en un encabezado HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9a60-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="b9a60-266">En `Startup.Configure`, agregue el código siguiente antes de llamar a `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="b9a60-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="b9a60-267">Configure el middleware de reenvío de certificados para especificar el nombre del encabezado.</span><span class="sxs-lookup"><span data-stu-id="b9a60-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="b9a60-268">En `Startup.ConfigureServices`, agregue el código siguiente para configurar el encabezado desde el que el middleware crea un certificado:</span><span class="sxs-lookup"><span data-stu-id="b9a60-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="b9a60-269">Si el proxy no codifica en Base64 el certificado (como ocurre con Nginx), establezca la opción `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="b9a60-270">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b9a60-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="b9a60-271">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b9a60-271">Troubleshoot</span></span>

<span data-ttu-id="b9a60-272">Cuando no se reenvíen los encabezados como estaba previsto, habilite el [registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b9a60-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b9a60-273">Si los registros no proporcionan suficiente información para solucionar el problema, enumere los encabezados de solicitud recibidos por el servidor.</span><span class="sxs-lookup"><span data-stu-id="b9a60-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="b9a60-274">Use middleware insertado para escribir encabezados de solicitud en la respuesta de una aplicación o para registrar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="b9a60-275">Para escribir los encabezados en la respuesta de la aplicación, coloque el siguiente middleware insertado terminal inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b9a60-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="b9a60-276">Puede escribir en registros en lugar de en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="b9a60-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="b9a60-277">Así el sitio funcionará con normalidad durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="b9a60-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="b9a60-278">Para ello, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="b9a60-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="b9a60-279">Inserte `ILogger<Startup>` en la clase `Startup` como se describe en [Creación de registros durante el inicio](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="b9a60-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="b9a60-280">Coloque el siguiente software intermedio insertado inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="b9a60-281">Si se procesa, los valores `X-Forwarded-{For|Proto|Host}` se trasladan a `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="b9a60-282">Si hay varios valores en un encabezado determinado, el middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="b9a60-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b9a60-283">El valor predeterminado de `ForwardLimit` es `1` (uno), por lo que solo el valor más a la derecha de los encabezados se procesa, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="b9a60-284">La dirección IP remota original de la solicitud debe coincidir con una entrada de las listas `KnownProxies` o `KnownNetworks` antes de procesar los encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="b9a60-285">Esto limita la suplantación de encabezados al no aceptarse reenviadores de servidores proxy que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="b9a60-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="b9a60-286">Cuando se detecta un servidor proxy desconocido, el registro indica la dirección de dicho proxy:</span><span class="sxs-lookup"><span data-stu-id="b9a60-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="b9a60-287">En el ejemplo anterior, 10.0.0.100 es un servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="b9a60-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="b9a60-288">Si se trata de un servidor proxy de confianza, agregue la dirección IP del servidor a `KnownProxies` o agregue una red de confianza a `KnownNetworks` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b9a60-289">Para más información, vea la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="b9a60-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="b9a60-290">Admita solo las redes y los servidores proxy de confianza para reenviar encabezados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="b9a60-291">De lo contrario, se pueden producir ataques de [suplantación de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="b9a60-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9a60-292">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b9a60-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="b9a60-293">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Microsoft Security Advisory CVE-2018-0787: Vulnerabilidad de elevación de privilegios de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="b9a60-293">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b9a60-294">En la configuración recomendada de ASP.NET Core, la aplicación se hospeda mediante IIS/módulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="b9a60-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="b9a60-295">Los servidores proxy, los equilibradores de carga y otros dispositivos de red con frecuencia ocultan información sobre la solicitud antes de que llegue a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b9a60-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="b9a60-296">Cuando las solicitudes HTTPS se redirigen mediante proxy a través de HTTP, el esquema original (HTTPS) se pierde y se debe reenviar en un encabezado.</span><span class="sxs-lookup"><span data-stu-id="b9a60-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="b9a60-297">Como una aplicación recibe una solicitud del proxy y no desde su verdadero origen en Internet o la red corporativa, la dirección IP del cliente de origen también se debe reenviar en el encabezado.</span><span class="sxs-lookup"><span data-stu-id="b9a60-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="b9a60-298">Esta información puede ser importante en el procesamiento de las solicitudes, por ejemplo, en los redireccionamientos, la autenticación, la generación de vínculos, la evaluación de directivas y la geolocalización del cliente.</span><span class="sxs-lookup"><span data-stu-id="b9a60-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="b9a60-299">Encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="b9a60-299">Forwarded headers</span></span>

<span data-ttu-id="b9a60-300">Por costumbre, los servidores proxy reenvían la información en encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="b9a60-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="b9a60-301">Header</span><span class="sxs-lookup"><span data-stu-id="b9a60-301">Header</span></span> | <span data-ttu-id="b9a60-302">Descripción</span><span class="sxs-lookup"><span data-stu-id="b9a60-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="b9a60-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="b9a60-303">X-Forwarded-For</span></span> | <span data-ttu-id="b9a60-304">Contiene información sobre el cliente que inició la solicitud y los servidores proxy posteriores en una cadena de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="b9a60-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="b9a60-305">Este parámetro puede contener direcciones IP (y, opcionalmente, números de puerto).</span><span class="sxs-lookup"><span data-stu-id="b9a60-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="b9a60-306">En una cadena de servidores proxy, el primer parámetro indica al cliente dónde se realizó primero la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b9a60-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="b9a60-307">Le siguen los identificadores de proxy posteriores.</span><span class="sxs-lookup"><span data-stu-id="b9a60-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="b9a60-308">El último proxy en la cadena no se encuentra en la lista de parámetros.</span><span class="sxs-lookup"><span data-stu-id="b9a60-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="b9a60-309">La última dirección IP del proxy y, opcionalmente, un número de puerto, está disponible como la dirección IP remota en la capa de transporte.</span><span class="sxs-lookup"><span data-stu-id="b9a60-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="b9a60-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="b9a60-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="b9a60-311">El valor del esquema de origen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b9a60-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="b9a60-312">El valor también puede ser una lista de esquemas si la solicitud ha pasado por varios servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="b9a60-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="b9a60-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="b9a60-313">X-Forwarded-Host</span></span> | <span data-ttu-id="b9a60-314">El valor original del campo de encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="b9a60-314">The original value of the Host header field.</span></span> <span data-ttu-id="b9a60-315">Por lo general, los servidores proxy no modifican el encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="b9a60-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="b9a60-316">Consulte [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para información sobre una vulnerabilidad de elevación de privilegios que afecta a sistemas donde el proxy no valida ni restringe los encabezados de host a valores buenos conocidos.</span><span class="sxs-lookup"><span data-stu-id="b9a60-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="b9a60-317">El Middleware de encabezados reenviados, del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lee estos encabezados y rellena los campos asociados en <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="b9a60-318">El middleware realiza las siguientes actualizaciones:</span><span class="sxs-lookup"><span data-stu-id="b9a60-318">The middleware updates:</span></span>

* <span data-ttu-id="b9a60-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): establézcalo mediante el valor de encabezado `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="b9a60-320">Los valores de configuración adicionales afectan a cómo el middleware establece `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="b9a60-321">Para más información, consulte la sección [Opciones de Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="b9a60-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="b9a60-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): establézcalo mediante el valor de encabezado `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="b9a60-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): establézcalo mediante el valor de encabezado `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="b9a60-324">Se pueden configurar los [valores predeterminados](#forwarded-headers-middleware-options) del Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="b9a60-325">Estos valores son:</span><span class="sxs-lookup"><span data-stu-id="b9a60-325">The default settings are:</span></span>

* <span data-ttu-id="b9a60-326">Solo hay *un proxy* entre la aplicación y el origen de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="b9a60-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="b9a60-327">Solo las direcciones de bucle invertido se configuran para servidores proxy conocidos y redes conocidas.</span><span class="sxs-lookup"><span data-stu-id="b9a60-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="b9a60-328">Los encabezados reenviados se denominan `X-Forwarded-For` y `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="b9a60-329">No todos los dispositivos de red agregan los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="b9a60-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="b9a60-330">Consulte las instrucciones del fabricante de su dispositivo si las solicitudes redirigidas mediante proxy no contienen estos encabezados cuando llegan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9a60-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="b9a60-331">Si el dispositivo usa nombres de encabezado distintos a `X-Forwarded-For` y `X-Forwarded-Proto`, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para que coincidan con los nombres de encabezado empleados por el dispositivo.</span><span class="sxs-lookup"><span data-stu-id="b9a60-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="b9a60-332">Para obtener más información, vea [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options) y [Configuración de un proxy que usa otros nombres de encabezado](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="b9a60-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="b9a60-333">IIS o IIS Express y el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9a60-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="b9a60-334">El Middleware de encabezados reenviados se habilita de forma predeterminada mediante el [Middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) cuando la aplicación se hospeda [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás de IIS y del módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9a60-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="b9a60-335">El Middleware de encabezados reenviados está activado para ejecutarse primero en la canalización de middleware con una configuración restringida específica del módulo ASP.NET Core debido a problemas de confianza con los encabezados reenviados (por ejemplo, [suplantación de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="b9a60-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="b9a60-336">El middleware está configurado para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` y está restringido a un único proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="b9a60-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="b9a60-337">Si se requiere configuración adicional, consulte la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="b9a60-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b9a60-338">Otros escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="b9a60-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b9a60-339">Al margen del uso de la [integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), el Middleware de encabezados reenviados no está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b9a60-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="b9a60-340">El middleware de encabezados reenviados debe estar habilitado en una aplicación para procesar los encabezados reenviados con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="b9a60-341">Después de habilitar el middleware, si no se especifica <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para él, el valor predeterminado [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="b9a60-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="b9a60-342">Configure el middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b9a60-343">Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure` antes de llamar a otro middleware:</span><span class="sxs-lookup"><span data-stu-id="b9a60-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="b9a60-344">Si no se especifica ningún <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> en `Startup.ConfigureServices` o directamente en el método de extensión con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, los encabezados predeterminados para reenviar son [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="b9a60-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="b9a60-345">La propiedad <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> debe estar configurada con los encabezados que se van a reenviar.</span><span class="sxs-lookup"><span data-stu-id="b9a60-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="b9a60-346">Configuración de Nginx</span><span class="sxs-lookup"><span data-stu-id="b9a60-346">Nginx configuration</span></span>

<span data-ttu-id="b9a60-347">Para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="b9a60-348">Para obtener más información, consulte [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: uso del encabezado Forwarded).</span><span class="sxs-lookup"><span data-stu-id="b9a60-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="b9a60-349">Configuración de Apache</span><span class="sxs-lookup"><span data-stu-id="b9a60-349">Apache configuration</span></span>

<span data-ttu-id="b9a60-350">`X-Forwarded-For` se agrega automáticamente. Consulte [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Módulo de Apache mod_proxy: Encabezados de solicitud de proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="b9a60-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="b9a60-351">Para obtener información sobre cómo reenviar el encabezado `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="b9a60-352">Opciones del Middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="b9a60-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="b9a60-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla el comportamiento del middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="b9a60-354">En el ejemplo siguiente se cambian los valores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="b9a60-354">The following example changes the default values:</span></span>

* <span data-ttu-id="b9a60-355">Limite el número de entradas de los encabezados reenviados a `2`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="b9a60-356">Agregue una dirección de proxy conocida de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="b9a60-357">Cambie el nombre del encabezado reenviado del valor predeterminado `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="b9a60-358">Opción</span><span class="sxs-lookup"><span data-stu-id="b9a60-358">Option</span></span> | <span data-ttu-id="b9a60-359">Descripción</span><span class="sxs-lookup"><span data-stu-id="b9a60-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="b9a60-360">Restringe los hosts por el encabezado `X-Forwarded-Host` a los valores proporcionados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="b9a60-361">Los valores se comparan mediante ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="b9a60-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="b9a60-362">Se deben excluir los números de puerto.</span><span class="sxs-lookup"><span data-stu-id="b9a60-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="b9a60-363">Si la lista está vacía, se permiten todos los hosts.</span><span class="sxs-lookup"><span data-stu-id="b9a60-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="b9a60-364">Un carácter comodín de nivel superior `*` permite que todos los hosts que no están vacíos.</span><span class="sxs-lookup"><span data-stu-id="b9a60-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="b9a60-365">Se permiten caracteres comodín de subdominio, pero no coinciden con el dominio raíz.</span><span class="sxs-lookup"><span data-stu-id="b9a60-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="b9a60-366">Por ejemplo, `*.contoso.com` coincide con el subdominio `foo.contoso.com` pero no con el dominio raíz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="b9a60-367">Se permiten nombres de host Unicode, pero se convierten en [Punycode](https://tools.ietf.org/html/rfc3492) para buscar la coincidencia.</span><span class="sxs-lookup"><span data-stu-id="b9a60-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="b9a60-368">Las [direcciones IPv6](https://tools.ietf.org/html/rfc4291) deben incluir corchetes de enlace y estar en [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por ejemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="b9a60-369">Las direcciones IPv6 no usan mayúsculas y minúsculas de forma especial para buscar la igualdad lógica entre diferentes formatos, y no se realiza ninguna canonización.</span><span class="sxs-lookup"><span data-stu-id="b9a60-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="b9a60-370">Si no se restringen los hosts permitidos, un atacante podría suplantar los vínculos generados por el servicio.</span><span class="sxs-lookup"><span data-stu-id="b9a60-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="b9a60-371">El valor predeterminado es un `IList<string>` vacío.</span><span class="sxs-lookup"><span data-stu-id="b9a60-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="b9a60-372">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="b9a60-373">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-For` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="b9a60-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b9a60-374">De manera predeterminada, es `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="b9a60-375">Identifica qué reenviadores se deben procesar.</span><span class="sxs-lookup"><span data-stu-id="b9a60-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="b9a60-376">Consulte [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para obtener la lista de campos que se aplican.</span><span class="sxs-lookup"><span data-stu-id="b9a60-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="b9a60-377">Los valores típicos que se asignan a esta propiedad son `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="b9a60-378">El valor predeterminado es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="b9a60-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="b9a60-379">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="b9a60-380">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Host` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="b9a60-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b9a60-381">De manera predeterminada, es `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="b9a60-382">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="b9a60-383">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Proto` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="b9a60-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b9a60-384">De manera predeterminada, es `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="b9a60-385">Limita el número de entradas en los encabezados que se procesan.</span><span class="sxs-lookup"><span data-stu-id="b9a60-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="b9a60-386">Establézcalo en `null` para deshabilitar el límite, pero esto solo se debe realizar si están configurados `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="b9a60-387">Establecerlo en un valor que no sea `null` es una medida de precaución (no una garantía) para protegerse contra los proxies mal configurados y las peticiones maliciosas que llegan desde los canales laterales de la red.</span><span class="sxs-lookup"><span data-stu-id="b9a60-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="b9a60-388">El middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="b9a60-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b9a60-389">Si se usa el valor predeterminado (`1`), solo se procesa el valor más a la derecha de los encabezados, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="b9a60-390">De manera predeterminada, es `1`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="b9a60-391">Intervalos de direcciones de redes conocidas de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="b9a60-392">Proporcione intervalos de direcciones IP mediante la notación de Enrutamiento de interdominios sin clases (CIDR).</span><span class="sxs-lookup"><span data-stu-id="b9a60-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="b9a60-393">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b9a60-394">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b9a60-395">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b9a60-396">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="b9a60-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b9a60-397">El valor predeterminado es un `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contiene una única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="b9a60-398">Direcciones de servidores proxy conocidos de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="b9a60-399">Use `KnownProxies` para especificar las coincidencias exactas de direcciones IP.</span><span class="sxs-lookup"><span data-stu-id="b9a60-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="b9a60-400">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b9a60-401">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b9a60-402">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b9a60-403">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="b9a60-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b9a60-404">El valor predeterminado es un `IList`\<<xref:System.Net.IPAddress>> que contiene una única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="b9a60-405">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="b9a60-406">De manera predeterminada, es `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="b9a60-407">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="b9a60-408">De manera predeterminada, es `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="b9a60-409">Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="b9a60-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="b9a60-410">De manera predeterminada, es `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="b9a60-411">Requiere que el número de valores de encabezado esté sincronizado entre los valores [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) que se van a procesar.</span><span class="sxs-lookup"><span data-stu-id="b9a60-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="b9a60-412">El valor predeterminado en ASP.NET Core 1.x es `true`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="b9a60-413">El valor predeterminado en ASP.NET Core 2.0 o posterior es `false`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="b9a60-414">Escenarios y casos de uso</span><span class="sxs-lookup"><span data-stu-id="b9a60-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="b9a60-415">Cuando no es posible agregar encabezados reenviados y todas las solicitudes son seguras</span><span class="sxs-lookup"><span data-stu-id="b9a60-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="b9a60-416">En algunos casos, puede que no sea posible agregar encabezados reenviados a las solicitudes redirigidas mediante proxy a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9a60-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="b9a60-417">Si el proxy está forzando a que todas las solicitudes externas públicas sean HTTPS, el esquema se puede establecer manualmente en `Startup.Configure` antes de usar cualquier tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="b9a60-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="b9a60-418">Este código puede deshabilitarse con una variable de entorno u otro valor de configuración en un entorno de desarrollo o ensayo.</span><span class="sxs-lookup"><span data-stu-id="b9a60-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="b9a60-419">Tratar con la ruta de acceso base y los servidores proxy que cambian la ruta de acceso de la solicitud</span><span class="sxs-lookup"><span data-stu-id="b9a60-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="b9a60-420">Algunos servidores proxy pasan la ruta de acceso sin cambios pero con una ruta de acceso base de aplicación que se debe quitar para que el enrutamiento funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="b9a60-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="b9a60-421">El middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide la ruta de acceso en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) y la ruta de acceso base de aplicación en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="b9a60-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="b9a60-422">Si `/foo` es la ruta de acceso base de aplicación para una ruta de acceso de proxy que se pasa como `/foo/api/1`, el middleware establece `Request.PathBase` en `/foo` y `Request.Path` en `/api/1` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="b9a60-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="b9a60-423">La ruta de acceso base y la ruta de acceso original se vuelven a aplicar cuando se llama de nuevo al middleware en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="b9a60-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="b9a60-424">Para obtener más información sobre el procesamiento de pedidos del middleware, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="b9a60-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="b9a60-425">Si el proxy recorta la ruta de acceso (por ejemplo, el reenvío `/foo/api/1` a `/api/1`), corrija los redireccionamientos y los vínculos mediante el establecimiento de la propiedad [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="b9a60-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="b9a60-426">Si el proxy va a agregar datos de ruta de acceso, descarte parte de esta ruta para corregir los redireccionamientos y los vínculos; para ello, use <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> y asígnelo a la propiedad <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="b9a60-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="b9a60-427">Configuración de un proxy que usa otros nombres de encabezado</span><span class="sxs-lookup"><span data-stu-id="b9a60-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="b9a60-428">Si el proxy no usa los encabezados denominados `X-Forwarded-For` y `X-Forwarded-Proto` para reenviar el puerto o la dirección de proxy y originar información de esquema, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> de modo que coincidan con los nombres de encabezado empleados por el proxy:</span><span class="sxs-lookup"><span data-stu-id="b9a60-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="b9a60-429">Configuración de una dirección IPv4 representada como una dirección IPv6</span><span class="sxs-lookup"><span data-stu-id="b9a60-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="b9a60-430">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="b9a60-431">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b9a60-432">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="b9a60-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="b9a60-433">En el ejemplo siguiente, se agrega una dirección de red que proporciona encabezados reenviados a la lista `KnownNetworks` en formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="b9a60-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="b9a60-434">Dirección IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="b9a60-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="b9a60-435">Dirección IPv6 convertida: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="b9a60-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="b9a60-436">Longitud de prefijo convertida: 104</span><span class="sxs-lookup"><span data-stu-id="b9a60-436">Converted prefix length: 104</span></span>

<span data-ttu-id="b9a60-437">También puede proporcionar la dirección en formato hexadecimal (`10.11.12.1` se representa en IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="b9a60-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="b9a60-438">Al convertir una dirección IPv4 en IPv6, agregue 96 a la longitud de prefijo de CIDR (`8` en el ejemplo) para tener en cuenta el prefijo de IPv6 `::ffff:` adicional (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="b9a60-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="b9a60-439">Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS</span><span class="sxs-lookup"><span data-stu-id="b9a60-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="b9a60-440">Las aplicaciones que llaman a los métodos <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> y <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> incluyen un sitio en un bucle infinito si se implementan en una instancia de Azure App Service de Linux, una máquina virtual Linux de Azure, o bien detrás de cualquier otro servidor proxy inverso, además de IIS.</span><span class="sxs-lookup"><span data-stu-id="b9a60-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="b9a60-441">El servidor proxy inverso termina TLS y Kestrel no es consciente del esquema de solicitud correcto.</span><span class="sxs-lookup"><span data-stu-id="b9a60-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="b9a60-442">En esta configuración también se produce un error de OAuth y OIDC, ya que generan redirecciones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="b9a60-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="b9a60-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> agrega y configura middleware de encabezados reenviados cuando se ejecuta detrás de IIS, pero no hay ninguna configuración automática coincidente para Linux (integración de Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="b9a60-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="b9a60-444">Para reenviar el esquema desde el servidor proxy en escenarios que no sean de IIS, agregue y configure Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="b9a60-445">En `Startup.ConfigureServices`, use el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="b9a60-445">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="b9a60-446">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="b9a60-446">Troubleshoot</span></span>

<span data-ttu-id="b9a60-447">Cuando no se reenvíen los encabezados como estaba previsto, habilite el [registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b9a60-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b9a60-448">Si los registros no proporcionan suficiente información para solucionar el problema, enumere los encabezados de solicitud recibidos por el servidor.</span><span class="sxs-lookup"><span data-stu-id="b9a60-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="b9a60-449">Use middleware insertado para escribir encabezados de solicitud en la respuesta de una aplicación o para registrar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="b9a60-450">Para escribir los encabezados en la respuesta de la aplicación, coloque el siguiente middleware insertado terminal inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b9a60-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="b9a60-451">Puede escribir en registros en lugar de en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="b9a60-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="b9a60-452">Así el sitio funcionará con normalidad durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="b9a60-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="b9a60-453">Para ello, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="b9a60-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="b9a60-454">Inserte `ILogger<Startup>` en la clase `Startup` como se describe en [Creación de registros durante el inicio](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="b9a60-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="b9a60-455">Coloque el siguiente software intermedio insertado inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="b9a60-456">Si se procesa, los valores `X-Forwarded-{For|Proto|Host}` se trasladan a `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="b9a60-457">Si hay varios valores en un encabezado determinado, el middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="b9a60-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b9a60-458">El valor predeterminado de `ForwardLimit` es `1` (uno), por lo que solo el valor más a la derecha de los encabezados se procesa, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="b9a60-459">La dirección IP remota original de la solicitud debe coincidir con una entrada de las listas `KnownProxies` o `KnownNetworks` antes de procesar los encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="b9a60-460">Esto limita la suplantación de encabezados al no aceptarse reenviadores de servidores proxy que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="b9a60-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="b9a60-461">Cuando se detecta un servidor proxy desconocido, el registro indica la dirección de dicho proxy:</span><span class="sxs-lookup"><span data-stu-id="b9a60-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="b9a60-462">En el ejemplo anterior, 10.0.0.100 es un servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="b9a60-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="b9a60-463">Si se trata de un servidor proxy de confianza, agregue la dirección IP del servidor a `KnownProxies` o agregue una red de confianza a `KnownNetworks` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b9a60-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b9a60-464">Para más información, vea la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="b9a60-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="b9a60-465">Admita solo las redes y los servidores proxy de confianza para reenviar encabezados.</span><span class="sxs-lookup"><span data-stu-id="b9a60-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="b9a60-466">De lo contrario, se pueden producir ataques de [suplantación de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="b9a60-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9a60-467">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b9a60-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="b9a60-468">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Microsoft Security Advisory CVE-2018-0787: Vulnerabilidad de elevación de privilegios de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="b9a60-468">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
