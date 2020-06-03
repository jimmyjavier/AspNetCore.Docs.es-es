---
<span data-ttu-id="d08fb-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-102">'Blazor'</span></span>
- <span data-ttu-id="d08fb-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-103">'Identity'</span></span>
- <span data-ttu-id="d08fb-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-105">'Razor'</span></span>
- <span data-ttu-id="d08fb-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="d08fb-107">Configuración de ASP.NET Core para trabajar con servidores proxy y equilibradores de carga</span><span class="sxs-lookup"><span data-stu-id="d08fb-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="d08fb-108">Por [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="d08fb-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d08fb-109">En la configuración recomendada de ASP.NET Core, la aplicación se hospeda mediante IIS/módulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="d08fb-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="d08fb-110">Los servidores proxy, los equilibradores de carga y otros dispositivos de red con frecuencia ocultan información sobre la solicitud antes de que llegue a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d08fb-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="d08fb-111">Cuando las solicitudes HTTPS se redirigen mediante proxy a través de HTTP, el esquema original (HTTPS) se pierde y se debe reenviar en un encabezado.</span><span class="sxs-lookup"><span data-stu-id="d08fb-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="d08fb-112">Como una aplicación recibe una solicitud del proxy y no desde su verdadero origen en Internet o la red corporativa, la dirección IP del cliente de origen también se debe reenviar en el encabezado.</span><span class="sxs-lookup"><span data-stu-id="d08fb-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="d08fb-113">Esta información puede ser importante en el procesamiento de las solicitudes, por ejemplo, en los redireccionamientos, la autenticación, la generación de vínculos, la evaluación de directivas y la geolocalización del cliente.</span><span class="sxs-lookup"><span data-stu-id="d08fb-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="d08fb-114">Encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="d08fb-114">Forwarded headers</span></span>

<span data-ttu-id="d08fb-115">Por costumbre, los servidores proxy reenvían la información en encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="d08fb-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="d08fb-116">Header</span><span class="sxs-lookup"><span data-stu-id="d08fb-116">Header</span></span> | <span data-ttu-id="d08fb-117">Descripción</span><span class="sxs-lookup"><span data-stu-id="d08fb-117">Description</span></span> |
| ---
<span data-ttu-id="d08fb-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-119">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-119">'Blazor'</span></span>
- <span data-ttu-id="d08fb-120">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-120">'Identity'</span></span>
- <span data-ttu-id="d08fb-121">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-122">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-122">'Razor'</span></span>
- <span data-ttu-id="d08fb-123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-123">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-125">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-125">'Blazor'</span></span>
- <span data-ttu-id="d08fb-126">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-126">'Identity'</span></span>
- <span data-ttu-id="d08fb-127">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-128">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-128">'Razor'</span></span>
- <span data-ttu-id="d08fb-129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-131">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-131">'Blazor'</span></span>
- <span data-ttu-id="d08fb-132">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-132">'Identity'</span></span>
- <span data-ttu-id="d08fb-133">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-134">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-134">'Razor'</span></span>
- <span data-ttu-id="d08fb-135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-137">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-137">'Blazor'</span></span>
- <span data-ttu-id="d08fb-138">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-138">'Identity'</span></span>
- <span data-ttu-id="d08fb-139">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-140">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-140">'Razor'</span></span>
- <span data-ttu-id="d08fb-141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-141">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-142">------ | | X-Forwarded-For | Contiene información sobre el cliente que inició la solicitud y los servidores proxy posteriores en una cadena de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="d08fb-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="d08fb-143">Este parámetro puede contener direcciones IP (y, opcionalmente, números de puerto).</span><span class="sxs-lookup"><span data-stu-id="d08fb-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="d08fb-144">En una cadena de servidores proxy, el primer parámetro indica al cliente dónde se realizó primero la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d08fb-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="d08fb-145">Le siguen los identificadores de proxy posteriores.</span><span class="sxs-lookup"><span data-stu-id="d08fb-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="d08fb-146">El último proxy en la cadena no se encuentra en la lista de parámetros.</span><span class="sxs-lookup"><span data-stu-id="d08fb-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="d08fb-147">La última dirección IP del proxy y, opcionalmente, un número de puerto, está disponible como la dirección IP remota en la capa de transporte.</span><span class="sxs-lookup"><span data-stu-id="d08fb-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="d08fb-148">| | X-Forwarded-Proto | El valor del esquema de origen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d08fb-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="d08fb-149">El valor también puede ser una lista de esquemas si la solicitud ha pasado por varios servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="d08fb-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="d08fb-150">| | X-Forwarded-Host | El valor original del campo de encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="d08fb-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="d08fb-151">Por lo general, los servidores proxy no modifican el encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="d08fb-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="d08fb-152">Consulte [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para información sobre una vulnerabilidad de elevación de privilegios que afecta a sistemas donde el proxy no valida ni restringe los encabezados de host a valores buenos conocidos.</span><span class="sxs-lookup"><span data-stu-id="d08fb-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="d08fb-153">El Middleware de encabezados reenviados, del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lee estos encabezados y rellena los campos asociados en <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="d08fb-154">El middleware realiza las siguientes actualizaciones:</span><span class="sxs-lookup"><span data-stu-id="d08fb-154">The middleware updates:</span></span>

* <span data-ttu-id="d08fb-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): establézcala mediante el valor de encabezado `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="d08fb-156">Los valores de configuración adicionales afectan a cómo el middleware establece `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="d08fb-157">Para más información, consulte la sección [Opciones de Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d08fb-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="d08fb-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): establézcalo mediante el valor de encabezado `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="d08fb-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): establézcalo mediante el valor de encabezado `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="d08fb-160">Se pueden configurar los [valores predeterminados](#forwarded-headers-middleware-options) del Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="d08fb-161">Estos valores son:</span><span class="sxs-lookup"><span data-stu-id="d08fb-161">The default settings are:</span></span>

* <span data-ttu-id="d08fb-162">Solo hay *un proxy* entre la aplicación y el origen de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d08fb-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="d08fb-163">Solo las direcciones de bucle invertido se configuran para servidores proxy conocidos y redes conocidas.</span><span class="sxs-lookup"><span data-stu-id="d08fb-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="d08fb-164">Los encabezados reenviados se denominan `X-Forwarded-For` y `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="d08fb-165">No todos los dispositivos de red agregan los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="d08fb-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="d08fb-166">Consulte las instrucciones del fabricante de su dispositivo si las solicitudes redirigidas mediante proxy no contienen estos encabezados cuando llegan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d08fb-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="d08fb-167">Si el dispositivo usa nombres de encabezado distintos a `X-Forwarded-For` y `X-Forwarded-Proto`, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para que coincidan con los nombres de encabezado empleados por el dispositivo.</span><span class="sxs-lookup"><span data-stu-id="d08fb-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="d08fb-168">Para obtener más información, vea [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options) y [Configuración de un proxy que usa otros nombres de encabezado](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="d08fb-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="d08fb-169">IIS o IIS Express y el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d08fb-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="d08fb-170">El Middleware de encabezados reenviados se habilita de forma predeterminada mediante el [Middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) cuando la aplicación se hospeda [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás de IIS y del módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d08fb-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="d08fb-171">El Middleware de encabezados reenviados está activado para ejecutarse primero en la canalización de middleware con una configuración restringida específica del módulo ASP.NET Core debido a problemas de confianza con los encabezados reenviados (por ejemplo, [suplantación de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="d08fb-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="d08fb-172">El middleware está configurado para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` y está restringido a un único proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="d08fb-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="d08fb-173">Si se requiere configuración adicional, consulte la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d08fb-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d08fb-174">Otros escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="d08fb-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d08fb-175">Al margen del uso de la [integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), el Middleware de encabezados reenviados no está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d08fb-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="d08fb-176">El middleware de encabezados reenviados debe estar habilitado en una aplicación para procesar los encabezados reenviados con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="d08fb-177">Después de habilitar el middleware, si no se especifica <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para él, el valor predeterminado [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d08fb-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="d08fb-178">Configure el middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d08fb-179">Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure` antes de llamar a otro middleware:</span><span class="sxs-lookup"><span data-stu-id="d08fb-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="d08fb-180">Si no se especifica ningún <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> en `Startup.ConfigureServices` o directamente en el método de extensión con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, los encabezados predeterminados para reenviar son [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d08fb-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="d08fb-181">La propiedad <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> debe estar configurada con los encabezados que se van a reenviar.</span><span class="sxs-lookup"><span data-stu-id="d08fb-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="d08fb-182">Configuración de Nginx</span><span class="sxs-lookup"><span data-stu-id="d08fb-182">Nginx configuration</span></span>

<span data-ttu-id="d08fb-183">Para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="d08fb-184">Para obtener más información, consulte [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: uso del encabezado Forwarded).</span><span class="sxs-lookup"><span data-stu-id="d08fb-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="d08fb-185">Configuración de Apache</span><span class="sxs-lookup"><span data-stu-id="d08fb-185">Apache configuration</span></span>

<span data-ttu-id="d08fb-186">`X-Forwarded-For` se agrega automáticamente. Consulte [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Módulo de Apache mod_proxy: Encabezados de solicitud de proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="d08fb-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="d08fb-187">Para obtener información sobre cómo reenviar el encabezado `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="d08fb-188">Opciones del Middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="d08fb-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="d08fb-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla el comportamiento del middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="d08fb-190">En el ejemplo siguiente se cambian los valores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="d08fb-190">The following example changes the default values:</span></span>

* <span data-ttu-id="d08fb-191">Limite el número de entradas de los encabezados reenviados a `2`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="d08fb-192">Agregue una dirección de proxy conocida de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="d08fb-193">Cambie el nombre del encabezado reenviado del valor predeterminado `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="d08fb-194">Opción</span><span class="sxs-lookup"><span data-stu-id="d08fb-194">Option</span></span> | <span data-ttu-id="d08fb-195">Descripción</span><span class="sxs-lookup"><span data-stu-id="d08fb-195">Description</span></span> |
| ---
<span data-ttu-id="d08fb-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-197">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-197">'Blazor'</span></span>
- <span data-ttu-id="d08fb-198">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-198">'Identity'</span></span>
- <span data-ttu-id="d08fb-199">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-200">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-200">'Razor'</span></span>
- <span data-ttu-id="d08fb-201">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-201">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-203">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-203">'Blazor'</span></span>
- <span data-ttu-id="d08fb-204">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-204">'Identity'</span></span>
- <span data-ttu-id="d08fb-205">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-206">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-206">'Razor'</span></span>
- <span data-ttu-id="d08fb-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-209">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-209">'Blazor'</span></span>
- <span data-ttu-id="d08fb-210">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-210">'Identity'</span></span>
- <span data-ttu-id="d08fb-211">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-212">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-212">'Razor'</span></span>
- <span data-ttu-id="d08fb-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-215">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-215">'Blazor'</span></span>
- <span data-ttu-id="d08fb-216">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-216">'Identity'</span></span>
- <span data-ttu-id="d08fb-217">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-218">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-218">'Razor'</span></span>
- <span data-ttu-id="d08fb-219">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-219">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restringe los hosts por el encabezado `X-Forwarded-Host` a los valores proporcionados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="d08fb-221">Los valores se comparan mediante ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="d08fb-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="d08fb-222">Se deben excluir los números de puerto.</span><span class="sxs-lookup"><span data-stu-id="d08fb-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="d08fb-223">Si la lista está vacía, se permiten todos los hosts.</span><span class="sxs-lookup"><span data-stu-id="d08fb-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="d08fb-224">Un carácter comodín de nivel superior `*` permite que todos los hosts que no están vacíos.</span><span class="sxs-lookup"><span data-stu-id="d08fb-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="d08fb-225">Se permiten caracteres comodín de subdominio, pero no coinciden con el dominio raíz.</span><span class="sxs-lookup"><span data-stu-id="d08fb-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="d08fb-226">Por ejemplo, `*.contoso.com` coincide con el subdominio `foo.contoso.com` pero no con el dominio raíz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="d08fb-227">Se permiten nombres de host Unicode, pero se convierten en [Punycode](https://tools.ietf.org/html/rfc3492) para buscar la coincidencia.</span><span class="sxs-lookup"><span data-stu-id="d08fb-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="d08fb-228">Las [direcciones IPv6](https://tools.ietf.org/html/rfc4291) deben incluir corchetes de enlace y estar en [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por ejemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="d08fb-229">Las direcciones IPv6 no usan mayúsculas y minúsculas de forma especial para buscar la igualdad lógica entre diferentes formatos, y no se realiza ninguna canonización.</span><span class="sxs-lookup"><span data-stu-id="d08fb-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="d08fb-230">Si no se restringen los hosts permitidos, un atacante podría suplantar los vínculos generados por el servicio.</span><span class="sxs-lookup"><span data-stu-id="d08fb-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="d08fb-231">El valor predeterminado es un `IList<string>` vacío.</span><span class="sxs-lookup"><span data-stu-id="d08fb-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="d08fb-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="d08fb-233">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-For` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="d08fb-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d08fb-234">De manera predeterminada, es `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="d08fb-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifica qué reenviadores se deben procesar.</span><span class="sxs-lookup"><span data-stu-id="d08fb-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="d08fb-236">Consulte [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para obtener la lista de campos que se aplican.</span><span class="sxs-lookup"><span data-stu-id="d08fb-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="d08fb-237">Los valores típicos que se asignan a esta propiedad son `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="d08fb-238">El valor predeterminado es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d08fb-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="d08fb-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="d08fb-240">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Host` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="d08fb-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d08fb-241">De manera predeterminada, es `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="d08fb-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="d08fb-243">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Proto` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="d08fb-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d08fb-244">De manera predeterminada, es `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="d08fb-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limita el número de entradas en los encabezados que se procesan.</span><span class="sxs-lookup"><span data-stu-id="d08fb-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="d08fb-246">Establézcalo en `null` para deshabilitar el límite, pero esto solo se debe realizar si están configurados `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="d08fb-247">Establecerlo en un valor que no sea `null` es una medida de precaución (no una garantía) para protegerse contra los proxies mal configurados y las peticiones maliciosas que llegan desde los canales laterales de la red.</span><span class="sxs-lookup"><span data-stu-id="d08fb-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="d08fb-248">El middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="d08fb-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d08fb-249">Si se usa el valor predeterminado (`1`), solo se procesa el valor más a la derecha de los encabezados, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="d08fb-250">De manera predeterminada, es `1`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-250">The default is `1`.</span></span> <span data-ttu-id="d08fb-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Intervalos de direcciones de redes conocidas de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="d08fb-252">Proporcione intervalos de direcciones IP mediante la notación de Enrutamiento de interdominios sin clases (CIDR).</span><span class="sxs-lookup"><span data-stu-id="d08fb-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="d08fb-253">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d08fb-254">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d08fb-255">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="d08fb-256">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="d08fb-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="d08fb-257">El valor predeterminado es un `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contiene una única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="d08fb-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Direcciones de servidores proxy conocidos de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="d08fb-259">Use `KnownProxies` para especificar las coincidencias exactas de direcciones IP.</span><span class="sxs-lookup"><span data-stu-id="d08fb-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="d08fb-260">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d08fb-261">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d08fb-262">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="d08fb-263">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="d08fb-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="d08fb-264">El valor predeterminado es un `IList`\<<xref:System.Net.IPAddress>> que contiene una única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="d08fb-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="d08fb-266">De manera predeterminada, es `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="d08fb-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="d08fb-268">De manera predeterminada, es `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="d08fb-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="d08fb-270">De manera predeterminada, es `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="d08fb-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Requiere que el número de valores de encabezado esté sincronizado entre los valores [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) que se van a procesar.</span><span class="sxs-lookup"><span data-stu-id="d08fb-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="d08fb-272">El valor predeterminado en ASP.NET Core 1.x es `true`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="d08fb-273">El valor predeterminado en ASP.NET Core 2.0 o posterior es `false`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="d08fb-274">Escenarios y casos de uso</span><span class="sxs-lookup"><span data-stu-id="d08fb-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="d08fb-275">Cuando no es posible agregar encabezados reenviados y todas las solicitudes son seguras</span><span class="sxs-lookup"><span data-stu-id="d08fb-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="d08fb-276">En algunos casos, puede que no sea posible agregar encabezados reenviados a las solicitudes redirigidas mediante proxy a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d08fb-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="d08fb-277">Si el proxy está forzando a que todas las solicitudes externas públicas sean HTTPS, el esquema se puede establecer manualmente en `Startup.Configure` antes de usar cualquier tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="d08fb-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="d08fb-278">Este código puede deshabilitarse con una variable de entorno u otro valor de configuración en un entorno de desarrollo o ensayo.</span><span class="sxs-lookup"><span data-stu-id="d08fb-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="d08fb-279">Tratar con la ruta de acceso base y los servidores proxy que cambian la ruta de acceso de la solicitud</span><span class="sxs-lookup"><span data-stu-id="d08fb-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="d08fb-280">Algunos servidores proxy pasan la ruta de acceso sin cambios pero con una ruta de acceso base de aplicación que se debe quitar para que el enrutamiento funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="d08fb-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="d08fb-281">El middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide la ruta de acceso en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) y la ruta de acceso base de aplicación en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="d08fb-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="d08fb-282">Si `/foo` es la ruta de acceso base de aplicación para una ruta de acceso de proxy que se pasa como `/foo/api/1`, el middleware establece `Request.PathBase` en `/foo` y `Request.Path` en `/api/1` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d08fb-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="d08fb-283">La ruta de acceso base y la ruta de acceso original se vuelven a aplicar cuando se llama de nuevo al middleware en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="d08fb-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="d08fb-284">Para obtener más información sobre el procesamiento de pedidos del middleware, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="d08fb-285">Si el proxy recorta la ruta de acceso (por ejemplo, el reenvío `/foo/api/1` a `/api/1`), corrija los redireccionamientos y los vínculos mediante el establecimiento de la propiedad [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="d08fb-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="d08fb-286">Si el proxy va a agregar datos de ruta de acceso, descarte parte de esta ruta para corregir los redireccionamientos y los vínculos; para ello, use <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> y asígnelo a la propiedad <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="d08fb-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="d08fb-287">Configuración de un proxy que usa otros nombres de encabezado</span><span class="sxs-lookup"><span data-stu-id="d08fb-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="d08fb-288">Si el proxy no usa los encabezados denominados `X-Forwarded-For` y `X-Forwarded-Proto` para reenviar el puerto o la dirección de proxy y originar información de esquema, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> de modo que coincidan con los nombres de encabezado empleados por el proxy:</span><span class="sxs-lookup"><span data-stu-id="d08fb-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="d08fb-289">Configuración de una dirección IPv4 representada como una dirección IPv6</span><span class="sxs-lookup"><span data-stu-id="d08fb-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="d08fb-290">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="d08fb-291">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d08fb-292">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="d08fb-293">En el ejemplo siguiente, se agrega una dirección de red que proporciona encabezados reenviados a la lista `KnownNetworks` en formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="d08fb-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="d08fb-294">Dirección IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="d08fb-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="d08fb-295">Dirección IPv6 convertida: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="d08fb-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="d08fb-296">Longitud de prefijo convertida: 104</span><span class="sxs-lookup"><span data-stu-id="d08fb-296">Converted prefix length: 104</span></span>

<span data-ttu-id="d08fb-297">También puede proporcionar la dirección en formato hexadecimal (`10.11.12.1` se representa en IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="d08fb-298">Al convertir una dirección IPv4 en IPv6, agregue 96 a la longitud de prefijo de CIDR (`8` en el ejemplo) para tener en cuenta el prefijo de IPv6 `::ffff:` adicional (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="d08fb-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="d08fb-299">Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS</span><span class="sxs-lookup"><span data-stu-id="d08fb-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="d08fb-300">Las aplicaciones que llaman a los métodos <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> y <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> incluyen un sitio en un bucle infinito si se implementan en una instancia de Azure App Service de Linux, una máquina virtual Linux de Azure, o bien detrás de cualquier otro servidor proxy inverso, además de IIS.</span><span class="sxs-lookup"><span data-stu-id="d08fb-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="d08fb-301">El servidor proxy inverso termina TLS y Kestrel no es consciente del esquema de solicitud correcto.</span><span class="sxs-lookup"><span data-stu-id="d08fb-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="d08fb-302">En esta configuración también se produce un error de OAuth y OIDC, ya que generan redirecciones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="d08fb-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="d08fb-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> agrega y configura middleware de encabezados reenviados cuando se ejecuta detrás de IIS, pero no hay ninguna configuración automática coincidente para Linux (integración de Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="d08fb-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="d08fb-304">Para reenviar el esquema desde el servidor proxy en escenarios que no sean de IIS, agregue y configure Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="d08fb-305">En `Startup.ConfigureServices`, use el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d08fb-305">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="d08fb-306">Reenvío de certificados</span><span class="sxs-lookup"><span data-stu-id="d08fb-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="d08fb-307">Azure</span><span class="sxs-lookup"><span data-stu-id="d08fb-307">Azure</span></span>

<span data-ttu-id="d08fb-308">Para configurar Azure App Service para el reenvío de certificados, consulte [Configuración de la autenticación mutua de TLS en Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="d08fb-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="d08fb-309">La guía siguiente se aplica a la configuración de la aplicación de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d08fb-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="d08fb-310">En `Startup.Configure`, agregue el código siguiente antes de llamar a `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="d08fb-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="d08fb-311">Configure el middleware de reenvío de certificados para especificar el nombre de encabezado que Azure usa.</span><span class="sxs-lookup"><span data-stu-id="d08fb-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="d08fb-312">En `Startup.ConfigureServices`, agregue el código siguiente para configurar el encabezado desde el que el middleware crea un certificado:</span><span class="sxs-lookup"><span data-stu-id="d08fb-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="d08fb-313">Otros servidores proxy web</span><span class="sxs-lookup"><span data-stu-id="d08fb-313">Other web proxies</span></span>

<span data-ttu-id="d08fb-314">Si se usa un proxy que no es IIS ni Enrutamiento de solicitud de aplicaciones de Azure App Service, configure el proxy para reenviar el certificado que recibió en un encabezado HTTP.</span><span class="sxs-lookup"><span data-stu-id="d08fb-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="d08fb-315">En `Startup.Configure`, agregue el código siguiente antes de llamar a `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="d08fb-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="d08fb-316">Configure el middleware de reenvío de certificados para especificar el nombre del encabezado.</span><span class="sxs-lookup"><span data-stu-id="d08fb-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="d08fb-317">En `Startup.ConfigureServices`, agregue el código siguiente para configurar el encabezado desde el que el middleware crea un certificado:</span><span class="sxs-lookup"><span data-stu-id="d08fb-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="d08fb-318">Si el proxy no codifica en Base64 el certificado (como ocurre con Nginx), establezca la opción `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="d08fb-319">Considere el ejemplo siguiente de `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d08fb-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="d08fb-320">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="d08fb-320">Troubleshoot</span></span>

<span data-ttu-id="d08fb-321">Cuando no se reenvíen los encabezados como estaba previsto, habilite el [registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d08fb-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d08fb-322">Si los registros no proporcionan suficiente información para solucionar el problema, enumere los encabezados de solicitud recibidos por el servidor.</span><span class="sxs-lookup"><span data-stu-id="d08fb-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="d08fb-323">Use middleware insertado para escribir encabezados de solicitud en la respuesta de una aplicación o para registrar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="d08fb-324">Para escribir los encabezados en la respuesta de la aplicación, coloque el siguiente middleware insertado terminal inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d08fb-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="d08fb-325">Puede escribir en registros en lugar de en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="d08fb-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="d08fb-326">Así el sitio funcionará con normalidad durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="d08fb-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="d08fb-327">Para ello, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="d08fb-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="d08fb-328">Inserte `ILogger<Startup>` en la clase `Startup` como se describe en [Creación de registros durante el inicio](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d08fb-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="d08fb-329">Coloque el siguiente software intermedio insertado inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d08fb-330">Si se procesa, los valores `X-Forwarded-{For|Proto|Host}` se trasladan a `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="d08fb-331">Si hay varios valores en un encabezado determinado, el middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="d08fb-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d08fb-332">El valor predeterminado de `ForwardLimit` es `1` (uno), por lo que solo el valor más a la derecha de los encabezados se procesa, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="d08fb-333">La dirección IP remota original de la solicitud debe coincidir con una entrada de las listas `KnownProxies` o `KnownNetworks` antes de procesar los encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="d08fb-334">Esto limita la suplantación de encabezados al no aceptarse reenviadores de servidores proxy que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="d08fb-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="d08fb-335">Cuando se detecta un servidor proxy desconocido, el registro indica la dirección de dicho proxy:</span><span class="sxs-lookup"><span data-stu-id="d08fb-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="d08fb-336">En el ejemplo anterior, 10.0.0.100 es un servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="d08fb-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="d08fb-337">Si se trata de un servidor proxy de confianza, agregue la dirección IP del servidor a `KnownProxies` o agregue una red de confianza a `KnownNetworks` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d08fb-338">Para más información, vea la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d08fb-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="d08fb-339">Admita solo las redes y los servidores proxy de confianza para reenviar encabezados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="d08fb-340">De lo contrario, se pueden producir ataques de [suplantación de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="d08fb-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d08fb-341">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d08fb-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="d08fb-342">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Microsoft Security Advisory CVE-2018-0787: Vulnerabilidad de elevación de privilegios de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d08fb-342">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d08fb-343">En la configuración recomendada de ASP.NET Core, la aplicación se hospeda mediante IIS/módulo ASP.NET Core, Nginx o Apache.</span><span class="sxs-lookup"><span data-stu-id="d08fb-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="d08fb-344">Los servidores proxy, los equilibradores de carga y otros dispositivos de red con frecuencia ocultan información sobre la solicitud antes de que llegue a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="d08fb-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="d08fb-345">Cuando las solicitudes HTTPS se redirigen mediante proxy a través de HTTP, el esquema original (HTTPS) se pierde y se debe reenviar en un encabezado.</span><span class="sxs-lookup"><span data-stu-id="d08fb-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="d08fb-346">Como una aplicación recibe una solicitud del proxy y no desde su verdadero origen en Internet o la red corporativa, la dirección IP del cliente de origen también se debe reenviar en el encabezado.</span><span class="sxs-lookup"><span data-stu-id="d08fb-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="d08fb-347">Esta información puede ser importante en el procesamiento de las solicitudes, por ejemplo, en los redireccionamientos, la autenticación, la generación de vínculos, la evaluación de directivas y la geolocalización del cliente.</span><span class="sxs-lookup"><span data-stu-id="d08fb-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="d08fb-348">Encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="d08fb-348">Forwarded headers</span></span>

<span data-ttu-id="d08fb-349">Por costumbre, los servidores proxy reenvían la información en encabezados HTTP.</span><span class="sxs-lookup"><span data-stu-id="d08fb-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="d08fb-350">Header</span><span class="sxs-lookup"><span data-stu-id="d08fb-350">Header</span></span> | <span data-ttu-id="d08fb-351">Descripción</span><span class="sxs-lookup"><span data-stu-id="d08fb-351">Description</span></span> |
| ---
<span data-ttu-id="d08fb-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-353">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-353">'Blazor'</span></span>
- <span data-ttu-id="d08fb-354">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-354">'Identity'</span></span>
- <span data-ttu-id="d08fb-355">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-356">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-356">'Razor'</span></span>
- <span data-ttu-id="d08fb-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-357">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-359">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-359">'Blazor'</span></span>
- <span data-ttu-id="d08fb-360">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-360">'Identity'</span></span>
- <span data-ttu-id="d08fb-361">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-362">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-362">'Razor'</span></span>
- <span data-ttu-id="d08fb-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-365">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-365">'Blazor'</span></span>
- <span data-ttu-id="d08fb-366">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-366">'Identity'</span></span>
- <span data-ttu-id="d08fb-367">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-368">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-368">'Razor'</span></span>
- <span data-ttu-id="d08fb-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-371">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-371">'Blazor'</span></span>
- <span data-ttu-id="d08fb-372">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-372">'Identity'</span></span>
- <span data-ttu-id="d08fb-373">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-374">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-374">'Razor'</span></span>
- <span data-ttu-id="d08fb-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-375">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-376">------ | | X-Forwarded-For | Contiene información sobre el cliente que inició la solicitud y los servidores proxy posteriores en una cadena de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="d08fb-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="d08fb-377">Este parámetro puede contener direcciones IP (y, opcionalmente, números de puerto).</span><span class="sxs-lookup"><span data-stu-id="d08fb-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="d08fb-378">En una cadena de servidores proxy, el primer parámetro indica al cliente dónde se realizó primero la solicitud.</span><span class="sxs-lookup"><span data-stu-id="d08fb-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="d08fb-379">Le siguen los identificadores de proxy posteriores.</span><span class="sxs-lookup"><span data-stu-id="d08fb-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="d08fb-380">El último proxy en la cadena no se encuentra en la lista de parámetros.</span><span class="sxs-lookup"><span data-stu-id="d08fb-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="d08fb-381">La última dirección IP del proxy y, opcionalmente, un número de puerto, está disponible como la dirección IP remota en la capa de transporte.</span><span class="sxs-lookup"><span data-stu-id="d08fb-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="d08fb-382">| | X-Forwarded-Proto | El valor del esquema de origen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d08fb-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="d08fb-383">El valor también puede ser una lista de esquemas si la solicitud ha pasado por varios servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="d08fb-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="d08fb-384">| | X-Forwarded-Host | El valor original del campo de encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="d08fb-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="d08fb-385">Por lo general, los servidores proxy no modifican el encabezado de host.</span><span class="sxs-lookup"><span data-stu-id="d08fb-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="d08fb-386">Consulte [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para información sobre una vulnerabilidad de elevación de privilegios que afecta a sistemas donde el proxy no valida ni restringe los encabezados de host a valores buenos conocidos.</span><span class="sxs-lookup"><span data-stu-id="d08fb-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="d08fb-387">El Middleware de encabezados reenviados, del paquete [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lee estos encabezados y rellena los campos asociados en <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="d08fb-388">El middleware realiza las siguientes actualizaciones:</span><span class="sxs-lookup"><span data-stu-id="d08fb-388">The middleware updates:</span></span>

* <span data-ttu-id="d08fb-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): establézcala mediante el valor de encabezado `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="d08fb-390">Los valores de configuración adicionales afectan a cómo el middleware establece `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="d08fb-391">Para más información, consulte la sección [Opciones de Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d08fb-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="d08fb-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): establézcalo mediante el valor de encabezado `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="d08fb-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): establézcalo mediante el valor de encabezado `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="d08fb-394">Se pueden configurar los [valores predeterminados](#forwarded-headers-middleware-options) del Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="d08fb-395">Estos valores son:</span><span class="sxs-lookup"><span data-stu-id="d08fb-395">The default settings are:</span></span>

* <span data-ttu-id="d08fb-396">Solo hay *un proxy* entre la aplicación y el origen de las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="d08fb-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="d08fb-397">Solo las direcciones de bucle invertido se configuran para servidores proxy conocidos y redes conocidas.</span><span class="sxs-lookup"><span data-stu-id="d08fb-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="d08fb-398">Los encabezados reenviados se denominan `X-Forwarded-For` y `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="d08fb-399">No todos los dispositivos de red agregan los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` sin configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="d08fb-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="d08fb-400">Consulte las instrucciones del fabricante de su dispositivo si las solicitudes redirigidas mediante proxy no contienen estos encabezados cuando llegan a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d08fb-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="d08fb-401">Si el dispositivo usa nombres de encabezado distintos a `X-Forwarded-For` y `X-Forwarded-Proto`, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para que coincidan con los nombres de encabezado empleados por el dispositivo.</span><span class="sxs-lookup"><span data-stu-id="d08fb-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="d08fb-402">Para obtener más información, vea [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options) y [Configuración de un proxy que usa otros nombres de encabezado](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="d08fb-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="d08fb-403">IIS o IIS Express y el módulo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d08fb-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="d08fb-404">El Middleware de encabezados reenviados se habilita de forma predeterminada mediante el [Middleware de integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) cuando la aplicación se hospeda [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model) detrás de IIS y del módulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d08fb-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="d08fb-405">El Middleware de encabezados reenviados está activado para ejecutarse primero en la canalización de middleware con una configuración restringida específica del módulo ASP.NET Core debido a problemas de confianza con los encabezados reenviados (por ejemplo, [suplantación de IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="d08fb-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="d08fb-406">El middleware está configurado para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` y está restringido a un único proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="d08fb-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="d08fb-407">Si se requiere configuración adicional, consulte la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d08fb-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d08fb-408">Otros escenarios de servidor proxy y equilibrador de carga</span><span class="sxs-lookup"><span data-stu-id="d08fb-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d08fb-409">Al margen del uso de la [integración con IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) al hospedar [fuera de proceso](xref:host-and-deploy/iis/index#out-of-process-hosting-model), el Middleware de encabezados reenviados no está habilitado de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="d08fb-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="d08fb-410">El middleware de encabezados reenviados debe estar habilitado en una aplicación para procesar los encabezados reenviados con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="d08fb-411">Después de habilitar el middleware, si no se especifica <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para él, el valor predeterminado [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d08fb-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="d08fb-412">Configure el middleware con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d08fb-413">Invoque el método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure` antes de llamar a otro middleware:</span><span class="sxs-lookup"><span data-stu-id="d08fb-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="d08fb-414">Si no se especifica ningún <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> en `Startup.ConfigureServices` o directamente en el método de extensión con <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, los encabezados predeterminados para reenviar son [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d08fb-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="d08fb-415">La propiedad <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> debe estar configurada con los encabezados que se van a reenviar.</span><span class="sxs-lookup"><span data-stu-id="d08fb-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="d08fb-416">Configuración de Nginx</span><span class="sxs-lookup"><span data-stu-id="d08fb-416">Nginx configuration</span></span>

<span data-ttu-id="d08fb-417">Para reenviar los encabezados `X-Forwarded-For` y `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="d08fb-418">Para obtener más información, consulte [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/) (NGINX: uso del encabezado Forwarded).</span><span class="sxs-lookup"><span data-stu-id="d08fb-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="d08fb-419">Configuración de Apache</span><span class="sxs-lookup"><span data-stu-id="d08fb-419">Apache configuration</span></span>

<span data-ttu-id="d08fb-420">`X-Forwarded-For` se agrega automáticamente. Consulte [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) (Módulo de Apache mod_proxy: Encabezados de solicitud de proxy inverso).</span><span class="sxs-lookup"><span data-stu-id="d08fb-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="d08fb-421">Para obtener información sobre cómo reenviar el encabezado `X-Forwarded-Proto`, vea <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="d08fb-422">Opciones del Middleware de encabezados reenviados</span><span class="sxs-lookup"><span data-stu-id="d08fb-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="d08fb-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla el comportamiento del middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="d08fb-424">En el ejemplo siguiente se cambian los valores predeterminados:</span><span class="sxs-lookup"><span data-stu-id="d08fb-424">The following example changes the default values:</span></span>

* <span data-ttu-id="d08fb-425">Limite el número de entradas de los encabezados reenviados a `2`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="d08fb-426">Agregue una dirección de proxy conocida de `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="d08fb-427">Cambie el nombre del encabezado reenviado del valor predeterminado `X-Forwarded-For` a `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="d08fb-428">Opción</span><span class="sxs-lookup"><span data-stu-id="d08fb-428">Option</span></span> | <span data-ttu-id="d08fb-429">Descripción</span><span class="sxs-lookup"><span data-stu-id="d08fb-429">Description</span></span> |
| ---
<span data-ttu-id="d08fb-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-431">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-431">'Blazor'</span></span>
- <span data-ttu-id="d08fb-432">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-432">'Identity'</span></span>
- <span data-ttu-id="d08fb-433">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-434">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-434">'Razor'</span></span>
- <span data-ttu-id="d08fb-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-435">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-437">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-437">'Blazor'</span></span>
- <span data-ttu-id="d08fb-438">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-438">'Identity'</span></span>
- <span data-ttu-id="d08fb-439">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-440">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-440">'Razor'</span></span>
- <span data-ttu-id="d08fb-441">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-443">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-443">'Blazor'</span></span>
- <span data-ttu-id="d08fb-444">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-444">'Identity'</span></span>
- <span data-ttu-id="d08fb-445">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-446">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-446">'Razor'</span></span>
- <span data-ttu-id="d08fb-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d08fb-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="d08fb-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d08fb-449">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-449">'Blazor'</span></span>
- <span data-ttu-id="d08fb-450">"Identity"</span><span class="sxs-lookup"><span data-stu-id="d08fb-450">'Identity'</span></span>
- <span data-ttu-id="d08fb-451">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="d08fb-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="d08fb-452">"Razor"</span><span class="sxs-lookup"><span data-stu-id="d08fb-452">'Razor'</span></span>
- <span data-ttu-id="d08fb-453">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="d08fb-453">'SignalR' uid:</span></span> 

<span data-ttu-id="d08fb-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restringe los hosts por el encabezado `X-Forwarded-Host` a los valores proporcionados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="d08fb-455">Los valores se comparan mediante ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="d08fb-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="d08fb-456">Se deben excluir los números de puerto.</span><span class="sxs-lookup"><span data-stu-id="d08fb-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="d08fb-457">Si la lista está vacía, se permiten todos los hosts.</span><span class="sxs-lookup"><span data-stu-id="d08fb-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="d08fb-458">Un carácter comodín de nivel superior `*` permite que todos los hosts que no están vacíos.</span><span class="sxs-lookup"><span data-stu-id="d08fb-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="d08fb-459">Se permiten caracteres comodín de subdominio, pero no coinciden con el dominio raíz.</span><span class="sxs-lookup"><span data-stu-id="d08fb-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="d08fb-460">Por ejemplo, `*.contoso.com` coincide con el subdominio `foo.contoso.com` pero no con el dominio raíz `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="d08fb-461">Se permiten nombres de host Unicode, pero se convierten en [Punycode](https://tools.ietf.org/html/rfc3492) para buscar la coincidencia.</span><span class="sxs-lookup"><span data-stu-id="d08fb-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="d08fb-462">Las [direcciones IPv6](https://tools.ietf.org/html/rfc4291) deben incluir corchetes de enlace y estar en [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por ejemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="d08fb-463">Las direcciones IPv6 no usan mayúsculas y minúsculas de forma especial para buscar la igualdad lógica entre diferentes formatos, y no se realiza ninguna canonización.</span><span class="sxs-lookup"><span data-stu-id="d08fb-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="d08fb-464">Si no se restringen los hosts permitidos, un atacante podría suplantar los vínculos generados por el servicio.</span><span class="sxs-lookup"><span data-stu-id="d08fb-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="d08fb-465">El valor predeterminado es un `IList<string>` vacío.</span><span class="sxs-lookup"><span data-stu-id="d08fb-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="d08fb-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="d08fb-467">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-For` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="d08fb-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d08fb-468">De manera predeterminada, es `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="d08fb-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifica qué reenviadores se deben procesar.</span><span class="sxs-lookup"><span data-stu-id="d08fb-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="d08fb-470">Consulte [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para obtener la lista de campos que se aplican.</span><span class="sxs-lookup"><span data-stu-id="d08fb-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="d08fb-471">Los valores típicos que se asignan a esta propiedad son `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="d08fb-472">El valor predeterminado es [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="d08fb-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="d08fb-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="d08fb-474">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Host` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="d08fb-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d08fb-475">De manera predeterminada, es `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="d08fb-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="d08fb-477">Esta opción se usa cuando el reenviador o proxy no emplea el encabezado `X-Forwarded-Proto` sino algún otro para reenviar la información.</span><span class="sxs-lookup"><span data-stu-id="d08fb-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="d08fb-478">De manera predeterminada, es `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="d08fb-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limita el número de entradas en los encabezados que se procesan.</span><span class="sxs-lookup"><span data-stu-id="d08fb-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="d08fb-480">Establézcalo en `null` para deshabilitar el límite, pero esto solo se debe realizar si están configurados `KnownProxies` o `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="d08fb-481">Establecerlo en un valor que no sea `null` es una medida de precaución (no una garantía) para protegerse contra los proxies mal configurados y las peticiones maliciosas que llegan desde los canales laterales de la red.</span><span class="sxs-lookup"><span data-stu-id="d08fb-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="d08fb-482">El middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="d08fb-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d08fb-483">Si se usa el valor predeterminado (`1`), solo se procesa el valor más a la derecha de los encabezados, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="d08fb-484">De manera predeterminada, es `1`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-484">The default is `1`.</span></span> <span data-ttu-id="d08fb-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Intervalos de direcciones de redes conocidas de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="d08fb-486">Proporcione intervalos de direcciones IP mediante la notación de Enrutamiento de interdominios sin clases (CIDR).</span><span class="sxs-lookup"><span data-stu-id="d08fb-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="d08fb-487">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d08fb-488">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d08fb-489">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="d08fb-490">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="d08fb-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="d08fb-491">El valor predeterminado es un `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contiene una única entrada para `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="d08fb-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Direcciones de servidores proxy conocidos de los que se aceptan encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="d08fb-493">Use `KnownProxies` para especificar las coincidencias exactas de direcciones IP.</span><span class="sxs-lookup"><span data-stu-id="d08fb-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="d08fb-494">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="d08fb-495">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d08fb-496">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="d08fb-497">Para más información, consulte la sección [Configuración de una dirección IPv4 representada como una dirección IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="d08fb-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="d08fb-498">El valor predeterminado es un `IList`\<<xref:System.Net.IPAddress>> que contiene una única entrada para `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="d08fb-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="d08fb-500">De manera predeterminada, es `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="d08fb-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="d08fb-502">De manera predeterminada, es `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="d08fb-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use el encabezado especificado por esta propiedad en lugar del especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="d08fb-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="d08fb-504">De manera predeterminada, es `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="d08fb-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Requiere que el número de valores de encabezado esté sincronizado entre los valores [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) que se van a procesar.</span><span class="sxs-lookup"><span data-stu-id="d08fb-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="d08fb-506">El valor predeterminado en ASP.NET Core 1.x es `true`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="d08fb-507">El valor predeterminado en ASP.NET Core 2.0 o posterior es `false`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="d08fb-508">Escenarios y casos de uso</span><span class="sxs-lookup"><span data-stu-id="d08fb-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="d08fb-509">Cuando no es posible agregar encabezados reenviados y todas las solicitudes son seguras</span><span class="sxs-lookup"><span data-stu-id="d08fb-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="d08fb-510">En algunos casos, puede que no sea posible agregar encabezados reenviados a las solicitudes redirigidas mediante proxy a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d08fb-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="d08fb-511">Si el proxy está forzando a que todas las solicitudes externas públicas sean HTTPS, el esquema se puede establecer manualmente en `Startup.Configure` antes de usar cualquier tipo de middleware:</span><span class="sxs-lookup"><span data-stu-id="d08fb-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="d08fb-512">Este código puede deshabilitarse con una variable de entorno u otro valor de configuración en un entorno de desarrollo o ensayo.</span><span class="sxs-lookup"><span data-stu-id="d08fb-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="d08fb-513">Tratar con la ruta de acceso base y los servidores proxy que cambian la ruta de acceso de la solicitud</span><span class="sxs-lookup"><span data-stu-id="d08fb-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="d08fb-514">Algunos servidores proxy pasan la ruta de acceso sin cambios pero con una ruta de acceso base de aplicación que se debe quitar para que el enrutamiento funcione correctamente.</span><span class="sxs-lookup"><span data-stu-id="d08fb-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="d08fb-515">El middleware [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide la ruta de acceso en [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) y la ruta de acceso base de aplicación en [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="d08fb-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="d08fb-516">Si `/foo` es la ruta de acceso base de aplicación para una ruta de acceso de proxy que se pasa como `/foo/api/1`, el middleware establece `Request.PathBase` en `/foo` y `Request.Path` en `/api/1` con el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="d08fb-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="d08fb-517">La ruta de acceso base y la ruta de acceso original se vuelven a aplicar cuando se llama de nuevo al middleware en orden inverso.</span><span class="sxs-lookup"><span data-stu-id="d08fb-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="d08fb-518">Para obtener más información sobre el procesamiento de pedidos del middleware, vea <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="d08fb-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="d08fb-519">Si el proxy recorta la ruta de acceso (por ejemplo, el reenvío `/foo/api/1` a `/api/1`), corrija los redireccionamientos y los vínculos mediante el establecimiento de la propiedad [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) de la solicitud:</span><span class="sxs-lookup"><span data-stu-id="d08fb-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="d08fb-520">Si el proxy va a agregar datos de ruta de acceso, descarte parte de esta ruta para corregir los redireccionamientos y los vínculos; para ello, use <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> y asígnelo a la propiedad <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="d08fb-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="d08fb-521">Configuración de un proxy que usa otros nombres de encabezado</span><span class="sxs-lookup"><span data-stu-id="d08fb-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="d08fb-522">Si el proxy no usa los encabezados denominados `X-Forwarded-For` y `X-Forwarded-Proto` para reenviar el puerto o la dirección de proxy y originar información de esquema, establezca las opciones <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> y <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> de modo que coincidan con los nombres de encabezado empleados por el proxy:</span><span class="sxs-lookup"><span data-stu-id="d08fb-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="d08fb-523">Configuración de una dirección IPv4 representada como una dirección IPv6</span><span class="sxs-lookup"><span data-stu-id="d08fb-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="d08fb-524">Si el servidor usa sockets en modo dual, las direcciones IPv4 se suministran en formato IPv6 (por ejemplo, `10.0.0.1` en IPv4 se representa en IPv6 como `::ffff:10.0.0.1` o `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="d08fb-525">Consulte [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="d08fb-526">Para determinar si este formato es necesario, examine [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="d08fb-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="d08fb-527">En el ejemplo siguiente, se agrega una dirección de red que proporciona encabezados reenviados a la lista `KnownNetworks` en formato IPv6.</span><span class="sxs-lookup"><span data-stu-id="d08fb-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="d08fb-528">Dirección IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="d08fb-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="d08fb-529">Dirección IPv6 convertida: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="d08fb-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="d08fb-530">Longitud de prefijo convertida: 104</span><span class="sxs-lookup"><span data-stu-id="d08fb-530">Converted prefix length: 104</span></span>

<span data-ttu-id="d08fb-531">También puede proporcionar la dirección en formato hexadecimal (`10.11.12.1` se representa en IPv6 como `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="d08fb-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="d08fb-532">Al convertir una dirección IPv4 en IPv6, agregue 96 a la longitud de prefijo de CIDR (`8` en el ejemplo) para tener en cuenta el prefijo de IPv6 `::ffff:` adicional (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="d08fb-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="d08fb-533">Reenvío del esquema para servidores proxy inversos Linux y que no son de IIS</span><span class="sxs-lookup"><span data-stu-id="d08fb-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="d08fb-534">Las aplicaciones que llaman a los métodos <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> y <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> incluyen un sitio en un bucle infinito si se implementan en una instancia de Azure App Service de Linux, una máquina virtual Linux de Azure, o bien detrás de cualquier otro servidor proxy inverso, además de IIS.</span><span class="sxs-lookup"><span data-stu-id="d08fb-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="d08fb-535">El servidor proxy inverso termina TLS y Kestrel no es consciente del esquema de solicitud correcto.</span><span class="sxs-lookup"><span data-stu-id="d08fb-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="d08fb-536">En esta configuración también se produce un error de OAuth y OIDC, ya que generan redirecciones incorrectas.</span><span class="sxs-lookup"><span data-stu-id="d08fb-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="d08fb-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> agrega y configura middleware de encabezados reenviados cuando se ejecuta detrás de IIS, pero no hay ninguna configuración automática coincidente para Linux (integración de Apache o Nginx).</span><span class="sxs-lookup"><span data-stu-id="d08fb-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="d08fb-538">Para reenviar el esquema desde el servidor proxy en escenarios que no sean de IIS, agregue y configure Middleware de encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="d08fb-539">En `Startup.ConfigureServices`, use el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="d08fb-539">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="d08fb-540">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="d08fb-540">Troubleshoot</span></span>

<span data-ttu-id="d08fb-541">Cuando no se reenvíen los encabezados como estaba previsto, habilite el [registro](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="d08fb-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d08fb-542">Si los registros no proporcionan suficiente información para solucionar el problema, enumere los encabezados de solicitud recibidos por el servidor.</span><span class="sxs-lookup"><span data-stu-id="d08fb-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="d08fb-543">Use middleware insertado para escribir encabezados de solicitud en la respuesta de una aplicación o para registrar los encabezados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="d08fb-544">Para escribir los encabezados en la respuesta de la aplicación, coloque el siguiente middleware insertado terminal inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d08fb-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="d08fb-545">Puede escribir en registros en lugar de en el cuerpo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="d08fb-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="d08fb-546">Así el sitio funcionará con normalidad durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="d08fb-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="d08fb-547">Para ello, siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="d08fb-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="d08fb-548">Inserte `ILogger<Startup>` en la clase `Startup` como se describe en [Creación de registros durante el inicio](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="d08fb-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="d08fb-549">Coloque el siguiente software intermedio insertado inmediatamente después de la llamada a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> en `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d08fb-550">Si se procesa, los valores `X-Forwarded-{For|Proto|Host}` se trasladan a `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="d08fb-551">Si hay varios valores en un encabezado determinado, el middleware de encabezados reenviados procesa los encabezados en orden inverso, de derecha a izquierda.</span><span class="sxs-lookup"><span data-stu-id="d08fb-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="d08fb-552">El valor predeterminado de `ForwardLimit` es `1` (uno), por lo que solo el valor más a la derecha de los encabezados se procesa, a menos que se aumente el valor de `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="d08fb-553">La dirección IP remota original de la solicitud debe coincidir con una entrada de las listas `KnownProxies` o `KnownNetworks` antes de procesar los encabezados reenviados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="d08fb-554">Esto limita la suplantación de encabezados al no aceptarse reenviadores de servidores proxy que no son de confianza.</span><span class="sxs-lookup"><span data-stu-id="d08fb-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="d08fb-555">Cuando se detecta un servidor proxy desconocido, el registro indica la dirección de dicho proxy:</span><span class="sxs-lookup"><span data-stu-id="d08fb-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="d08fb-556">En el ejemplo anterior, 10.0.0.100 es un servidor proxy.</span><span class="sxs-lookup"><span data-stu-id="d08fb-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="d08fb-557">Si se trata de un servidor proxy de confianza, agregue la dirección IP del servidor a `KnownProxies` o agregue una red de confianza a `KnownNetworks` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d08fb-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d08fb-558">Para más información, vea la sección [Opciones del Middleware de encabezados reenviados](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="d08fb-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="d08fb-559">Admita solo las redes y los servidores proxy de confianza para reenviar encabezados.</span><span class="sxs-lookup"><span data-stu-id="d08fb-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="d08fb-560">De lo contrario, se pueden producir ataques de [suplantación de IP](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="d08fb-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d08fb-561">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="d08fb-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="d08fb-562">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Microsoft Security Advisory CVE-2018-0787: Vulnerabilidad de elevación de privilegios de ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d08fb-562">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
