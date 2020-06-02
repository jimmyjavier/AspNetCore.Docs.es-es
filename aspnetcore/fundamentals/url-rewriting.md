---
<span data-ttu-id="8c22f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-102">'Blazor'</span></span>
- <span data-ttu-id="8c22f-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-103">'Identity'</span></span>
- <span data-ttu-id="8c22f-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-105">'Razor'</span></span>
- <span data-ttu-id="8c22f-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="8c22f-107">Middleware de reescritura de URL en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c22f-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="8c22f-108">Por [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="8c22f-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c22f-109">En este documento se ofrece una introducción a la reescritura de URL e instrucciones sobre cómo usar el middleware de reescritura de URL en aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c22f-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="8c22f-110">La reescritura de URL consiste en modificar varias URL de solicitud basadas en una o varias reglas predefinidas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="8c22f-111">La reescritura de URL crea una abstracción entre las ubicaciones de recursos y sus direcciones para que las ubicaciones y las direcciones no estén estrechamente vinculadas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="8c22f-112">La reescritura de URL es útil en varios escenarios para:</span><span class="sxs-lookup"><span data-stu-id="8c22f-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="8c22f-113">Mover o reemplazar recursos del servidor de forma temporal o permanente a la vez que se mantienen localizadores estables para esos recursos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="8c22f-114">Dividir el procesamiento de solicitudes entre otras aplicaciones o entre áreas de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="8c22f-115">Quitar, agregar o reorganizar segmentos de dirección URL en solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="8c22f-116">Optimizar direcciones URL públicas para la optimización del motor de búsqueda (SEO).</span><span class="sxs-lookup"><span data-stu-id="8c22f-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="8c22f-117">Permitir el uso de direcciones URL públicas descriptivas para ayudar a los visitantes a predecir el contenido devuelto mediante la solicitud de un recurso.</span><span class="sxs-lookup"><span data-stu-id="8c22f-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="8c22f-118">Redirigir solicitudes no seguras a puntos de conexión seguros.</span><span class="sxs-lookup"><span data-stu-id="8c22f-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="8c22f-119">Evitar la vinculación activa, en la que un sitio externo usa un recurso estático hospedado en otro sitio mediante la vinculación del recurso a su propio contenido.</span><span class="sxs-lookup"><span data-stu-id="8c22f-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="8c22f-120">La reescritura de URL puede reducir el rendimiento de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="8c22f-121">Cuando sea factible, limite el número y la complejidad de las reglas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="8c22f-122">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8c22f-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="8c22f-123">Redireccionamiento y reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="8c22f-124">La diferencia entre los términos *redirección de URL* y *reescritura de URL* es sutil, pero tiene importantes implicaciones para proporcionar recursos a los clientes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="8c22f-125">El middleware de reescritura de URL de ASP.NET Core es capaz de satisfacer las necesidades de ambos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="8c22f-126">Una *redirección de URL* implica una operación del lado cliente, donde al cliente se le indica que acceda a un recurso en una dirección distinta a la que ha solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="8c22f-127">Esto requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="8c22f-127">This requires a round trip to the server.</span></span> <span data-ttu-id="8c22f-128">La URL de redireccionamiento que se devuelve al cliente aparece en la barra de direcciones del explorador cuando el cliente realiza una nueva solicitud para el recurso.</span><span class="sxs-lookup"><span data-stu-id="8c22f-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="8c22f-129">Si `/resource` se *redirige* a `/different-resource`, el servidor responde que el cliente debe obtener el recurso en `/different-resource` con un código de estado que indica que la redirección es temporal o permanente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Se cambió temporalmente un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="8c22f-135">Al redirigir las solicitudes a otra dirección URL, se indica si la redirección es permanente o temporal mediante la especificación del código de estado con la respuesta:</span><span class="sxs-lookup"><span data-stu-id="8c22f-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="8c22f-136">El código de estado *301 - Movido definitivamente* se usa cuando el recurso tiene una nueva dirección URL permanente y se quiere indicar al cliente que todas las solicitudes futuras para el recurso deben usar la nueva dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="8c22f-137">*El cliente puede almacenar en caché la respuesta cuando se recibe un código de estado 301.*</span><span class="sxs-lookup"><span data-stu-id="8c22f-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="8c22f-138">El código de estado *302: encontrado* se usa cuando el redireccionamiento es temporal o en general está sujeto a cambios.</span><span class="sxs-lookup"><span data-stu-id="8c22f-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="8c22f-139">El código de estado 302 indica al cliente que no almacene la dirección URL y la use en el futuro.</span><span class="sxs-lookup"><span data-stu-id="8c22f-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="8c22f-140">Para obtener más información sobre los códigos de estado, consulte [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: definiciones de los códigos de estado).</span><span class="sxs-lookup"><span data-stu-id="8c22f-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="8c22f-141">La *reescritura de URL* es una operación del lado servidor que proporciona un recurso desde una dirección de recursos distinta a la que el cliente ha solicitado.</span><span class="sxs-lookup"><span data-stu-id="8c22f-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="8c22f-142">La reescritura de una dirección URL no requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="8c22f-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="8c22f-143">La dirección URL reescrita no se devuelve al cliente y no aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c22f-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="8c22f-144">Si `/resource` se *reescribe* como `/different-resource`, el servidor la obtiene *internamente* y devuelve el recurso en `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="8c22f-145">Aunque es posible que el cliente pueda recuperar el recurso en la dirección URL reescrita, no se le informa de que el recurso existe en la dirección URL reescrita cuando realiza su solicitud y recibe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Se cambió un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="8c22f-150">Aplicación de ejemplo de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-150">URL rewriting sample app</span></span>

<span data-ttu-id="8c22f-151">Puede explorar las características del middleware de reescritura de URL con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="8c22f-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="8c22f-152">La aplicación emplea las reglas de redireccionamiento y reescritura, y muestra la URL redirigida o reescrita para varios escenarios.</span><span class="sxs-lookup"><span data-stu-id="8c22f-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="8c22f-153">Cuándo usar el middleware de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="8c22f-154">Use el middleware de reescritura de URL cuando no pueda usar los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c22f-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="8c22f-155">Módulo de reescritura de URL con IIS en Windows Server</span><span class="sxs-lookup"><span data-stu-id="8c22f-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="8c22f-156">Módulo mod_rewrite de Apache en el servidor Apache</span><span class="sxs-lookup"><span data-stu-id="8c22f-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="8c22f-157">Reescritura de URL en Nginx</span><span class="sxs-lookup"><span data-stu-id="8c22f-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="8c22f-158">Además, use el middleware cuando la aplicación se hospede en el [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener).</span><span class="sxs-lookup"><span data-stu-id="8c22f-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="8c22f-159">Las principales razones para usar la tecnologías de reescritura de URL basadas en servidor en IIS, Apache y Nginx son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c22f-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="8c22f-160">El middleware no es compatible con todas las características de estos módulos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="8c22f-161">Algunas de las características de los módulos de servidor no funcionan con proyectos de ASP.NET Core, como las restricciones `IsFile` y `IsDirectory` del módulo de reescritura de IIS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="8c22f-162">En estos casos, es mejor usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="8c22f-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="8c22f-163">El rendimiento del middleware probablemente no coincida con el de los módulos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="8c22f-164">La única manera de saber con certeza con qué enfoque el rendimiento disminuye más, o si la disminución de este es insignificante, es mediante una comparación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="8c22f-165">Package</span><span class="sxs-lookup"><span data-stu-id="8c22f-165">Package</span></span>

<span data-ttu-id="8c22f-166">El middleware de reescritura de URL está disponible en el paquete [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), incluido implícitamente en las aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c22f-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="8c22f-167">Extensión y opciones</span><span class="sxs-lookup"><span data-stu-id="8c22f-167">Extension and options</span></span>

<span data-ttu-id="8c22f-168">Establezca las reglas de reescritura y redirección de URL mediante la creación de una instancia de la clase [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con métodos de extensión para cada una de las reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="8c22f-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="8c22f-169">Encadene varias reglas en el orden que quiera procesarlas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="8c22f-170">Las `RewriteOptions` se pasan al middleware de reescritura de URL cuando se agrega a la canalización de solicitudes con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="8c22f-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="8c22f-171">Redirigir solicitudes que distintas de www a www</span><span class="sxs-lookup"><span data-stu-id="8c22f-171">Redirect non-www to www</span></span>

<span data-ttu-id="8c22f-172">Hay tres opciones que permiten a la aplicación redirigir solicitudes distintas de `www` a `www`:</span><span class="sxs-lookup"><span data-stu-id="8c22f-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="8c22f-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: redirige permanentemente la solicitud al subdominio `www` si la solicitud es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="8c22f-174">Se redirige con un código de estado [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="8c22f-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="8c22f-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: redirige la solicitud al subdominio `www` si la solicitud de entrada es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="8c22f-176">Se redirige con un código de estado [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="8c22f-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="8c22f-177">Una sobrecarga permite proporcionar el código de estado para la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="8c22f-178">Use un campo de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para una asignación de código de estado.</span><span class="sxs-lookup"><span data-stu-id="8c22f-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="8c22f-179">Redirección de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-179">URL redirect</span></span>

<span data-ttu-id="8c22f-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirigir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="8c22f-181">El primer parámetro contiene la expresión regular para hacer coincidir la ruta de acceso de la URL entrante.</span><span class="sxs-lookup"><span data-stu-id="8c22f-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="8c22f-182">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="8c22f-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="8c22f-183">El tercer parámetro, si está presente, especifica el código de estado.</span><span class="sxs-lookup"><span data-stu-id="8c22f-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="8c22f-184">Si no se especifica el código de estado, el valor predeterminado es *302 - Encontrado*, lo que indica que el recurso se ha movido o reemplazado temporalmente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="8c22f-185">En un explorador con herramientas de desarrollo habilitadas, realice una solicitud a la aplicación de ejemplo con la ruta de acceso `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="8c22f-186">La expresión regular coincide con la ruta de acceso de la solicitud en `redirect-rule/(.*)` y la ruta de acceso se reemplaza con `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="8c22f-187">La dirección URL de redireccionamiento se devuelve al cliente con un código de estado *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="8c22f-188">El explorador realiza una solicitud nueva en la URL de redireccionamiento, que aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c22f-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="8c22f-189">Como ninguna de las reglas de la aplicación de ejemplo coincide con la dirección URL de redireccionamiento:</span><span class="sxs-lookup"><span data-stu-id="8c22f-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="8c22f-190">La segunda solicitud recibe una respuesta *200: correcto* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="8c22f-191">En el cuerpo de la respuesta se muestra la dirección URL de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="8c22f-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="8c22f-192">Se realiza un recorrido de ida y vuelta al servidor cuando se *redirige* una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="8c22f-193">Tenga cuidado al establecer las reglas de redirección.</span><span class="sxs-lookup"><span data-stu-id="8c22f-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="8c22f-194">Las reglas de redirección se evalúan en cada solicitud enviada a la aplicación, incluso después de una redirección.</span><span class="sxs-lookup"><span data-stu-id="8c22f-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="8c22f-195">Es fácil crear por error un *bucle de redirecciones infinitas*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="8c22f-196">Solicitud original: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8c22f-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="8c22f-198">La parte de la expresión incluida entre paréntesis se denomina *grupo de capturas*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="8c22f-199">El punto (`.`) de la expresión significa *buscar cualquier carácter*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="8c22f-200">El asterisco (`*`) indica *buscar el carácter precedente cero o más veces*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="8c22f-201">Por tanto, el grupo de capturas `(.*)` busca los dos últimos segmentos de la ruta de acceso de la URL, `1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="8c22f-202">Cualquier valor que se proporcione en la URL de la solicitud después de `redirect-rule/` es capturado por este grupo de capturas único.</span><span class="sxs-lookup"><span data-stu-id="8c22f-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="8c22f-203">En la cadena de reemplazo, se insertan los grupos capturados en la cadena con el signo de dólar (`$`) seguido del número de secuencia de la captura.</span><span class="sxs-lookup"><span data-stu-id="8c22f-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="8c22f-204">Se obtiene el valor del primer grupo de capturas con `$1`, el segundo con `$2`, y así siguen en secuencia para los grupos de capturas de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="8c22f-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="8c22f-205">Solo hay un grupo capturado en la expresión regular de la regla de redirección de la aplicación de ejemplo, por lo que solo hay un grupo insertado en la cadena de reemplazo, que es `$1`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="8c22f-206">Cuando se aplica la regla, la URL se convierte en `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="8c22f-207">Redirección de URL a un punto de conexión segura</span><span class="sxs-lookup"><span data-stu-id="8c22f-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="8c22f-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirigir las solicitudes HTTP al mismo host y ruta de acceso mediante el protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="8c22f-209">Si no se proporciona el código de estado, el middleware muestra de forma predeterminada *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="8c22f-210">Si no se proporciona el puerto:</span><span class="sxs-lookup"><span data-stu-id="8c22f-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="8c22f-211">El middleware tiene como valor predeterminado `null`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="8c22f-212">El esquema cambia a `https` (protocolo HTTPS), y el cliente accede al recurso en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="8c22f-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="8c22f-213">En el ejemplo siguiente se muestra cómo establecer el código de estado en *301 - Movido definitivamente* y cambiar el puerto a 5001.</span><span class="sxs-lookup"><span data-stu-id="8c22f-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="8c22f-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirigir las solicitudes no seguras al mismo host y ruta de acceso mediante el protocolo HTTPS seguro en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="8c22f-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="8c22f-215">El middleware establece el código de estado en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="8c22f-216">Al redirigir a un punto de conexión seguro sin la necesidad de reglas de redirección adicionales, se recomienda usar el Middleware de redirección de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="8c22f-217">Para más información, vea el tema [Aplicación de HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="8c22f-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="8c22f-218">La aplicación de ejemplo es capaz de mostrar cómo usar `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="8c22f-219">Agregue el método de extensión a `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="8c22f-220">Realice una solicitud poco segura a la aplicación en cualquier URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="8c22f-221">Descarte la advertencia de seguridad del explorador que indica que el certificado autofirmado no es de confianza o cree una excepción para confiar en el certificado en cuestión.</span><span class="sxs-lookup"><span data-stu-id="8c22f-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="8c22f-222">Solicitud original mediante `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8c22f-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="8c22f-224">Solicitud original mediante `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8c22f-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="8c22f-226">Reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-226">URL rewrite</span></span>

<span data-ttu-id="8c22f-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para crear una regla para reescribir URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="8c22f-228">El primer parámetro contiene la expresión regular para buscar coincidencias en la ruta de dirección de URL entrante.</span><span class="sxs-lookup"><span data-stu-id="8c22f-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="8c22f-229">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="8c22f-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="8c22f-230">El tercer parámetro, `skipRemainingRules: {true|false}`, indica al middleware si al aplicar la regla actual tiene que omitir o no alguna regla de redirección adicional.</span><span class="sxs-lookup"><span data-stu-id="8c22f-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="8c22f-231">Solicitud original: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8c22f-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="8c22f-233">El acento circunflejo (`^`) al principio de la expresión significa que la búsqueda de coincidencias empieza al principio de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="8c22f-234">En el ejemplo anterior de la regla de redireccionamiento (`redirect-rule/(.*)`), no hay ningún acento circunflejo (`^`) al principio de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="8c22f-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="8c22f-235">Por tanto, cualquier carácter puede preceder a `redirect-rule/` en la ruta de acceso para que la coincidencia sea correcta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="8c22f-236">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="8c22f-236">Path</span></span>                               | <span data-ttu-id="8c22f-237">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="8c22f-237">Match</span></span> |
| ---
<span data-ttu-id="8c22f-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-239">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-239">'Blazor'</span></span>
- <span data-ttu-id="8c22f-240">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-240">'Identity'</span></span>
- <span data-ttu-id="8c22f-241">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-242">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-242">'Razor'</span></span>
- <span data-ttu-id="8c22f-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-245">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-245">'Blazor'</span></span>
- <span data-ttu-id="8c22f-246">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-246">'Identity'</span></span>
- <span data-ttu-id="8c22f-247">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-248">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-248">'Razor'</span></span>
- <span data-ttu-id="8c22f-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-251">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-251">'Blazor'</span></span>
- <span data-ttu-id="8c22f-252">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-252">'Identity'</span></span>
- <span data-ttu-id="8c22f-253">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-254">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-254">'Razor'</span></span>
- <span data-ttu-id="8c22f-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-257">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-257">'Blazor'</span></span>
- <span data-ttu-id="8c22f-258">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-258">'Identity'</span></span>
- <span data-ttu-id="8c22f-259">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-260">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-260">'Razor'</span></span>
- <span data-ttu-id="8c22f-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-263">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-263">'Blazor'</span></span>
- <span data-ttu-id="8c22f-264">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-264">'Identity'</span></span>
- <span data-ttu-id="8c22f-265">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-266">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-266">'Razor'</span></span>
- <span data-ttu-id="8c22f-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-269">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-269">'Blazor'</span></span>
- <span data-ttu-id="8c22f-270">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-270">'Identity'</span></span>
- <span data-ttu-id="8c22f-271">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-272">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-272">'Razor'</span></span>
- <span data-ttu-id="8c22f-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-275">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-275">'Blazor'</span></span>
- <span data-ttu-id="8c22f-276">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-276">'Identity'</span></span>
- <span data-ttu-id="8c22f-277">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-278">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-278">'Razor'</span></span>
- <span data-ttu-id="8c22f-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-281">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-281">'Blazor'</span></span>
- <span data-ttu-id="8c22f-282">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-282">'Identity'</span></span>
- <span data-ttu-id="8c22f-283">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-284">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-284">'Razor'</span></span>
- <span data-ttu-id="8c22f-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-287">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-287">'Blazor'</span></span>
- <span data-ttu-id="8c22f-288">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-288">'Identity'</span></span>
- <span data-ttu-id="8c22f-289">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-290">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-290">'Razor'</span></span>
- <span data-ttu-id="8c22f-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-293">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-293">'Blazor'</span></span>
- <span data-ttu-id="8c22f-294">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-294">'Identity'</span></span>
- <span data-ttu-id="8c22f-295">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-296">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-296">'Razor'</span></span>
- <span data-ttu-id="8c22f-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-299">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-299">'Blazor'</span></span>
- <span data-ttu-id="8c22f-300">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-300">'Identity'</span></span>
- <span data-ttu-id="8c22f-301">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-302">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-302">'Razor'</span></span>
- <span data-ttu-id="8c22f-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-305">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-305">'Blazor'</span></span>
- <span data-ttu-id="8c22f-306">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-306">'Identity'</span></span>
- <span data-ttu-id="8c22f-307">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-308">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-308">'Razor'</span></span>
- <span data-ttu-id="8c22f-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-311">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-311">'Blazor'</span></span>
- <span data-ttu-id="8c22f-312">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-312">'Identity'</span></span>
- <span data-ttu-id="8c22f-313">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-314">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-314">'Razor'</span></span>
- <span data-ttu-id="8c22f-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-317">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-317">'Blazor'</span></span>
- <span data-ttu-id="8c22f-318">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-318">'Identity'</span></span>
- <span data-ttu-id="8c22f-319">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-320">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-320">'Razor'</span></span>
- <span data-ttu-id="8c22f-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-323">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-323">'Blazor'</span></span>
- <span data-ttu-id="8c22f-324">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-324">'Identity'</span></span>
- <span data-ttu-id="8c22f-325">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-326">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-326">'Razor'</span></span>
- <span data-ttu-id="8c22f-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-327">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sí   | | `/my-cool-redirect-rule/1234/5678` | Sí   | | `/anotherredirect-rule/1234/5678`  | Sí   |</span><span class="sxs-lookup"><span data-stu-id="8c22f-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="8c22f-329">La regla de reescritura, `^rewrite-rule/(\d+)/(\d+)`, solo encuentra rutas de acceso que empiezan con `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="8c22f-330">En la tabla siguiente, observe la diferencia de coincidencia.</span><span class="sxs-lookup"><span data-stu-id="8c22f-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="8c22f-331">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="8c22f-331">Path</span></span>                              | <span data-ttu-id="8c22f-332">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="8c22f-332">Match</span></span> |
| ---
<span data-ttu-id="8c22f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-334">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-334">'Blazor'</span></span>
- <span data-ttu-id="8c22f-335">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-335">'Identity'</span></span>
- <span data-ttu-id="8c22f-336">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-337">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-337">'Razor'</span></span>
- <span data-ttu-id="8c22f-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-340">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-340">'Blazor'</span></span>
- <span data-ttu-id="8c22f-341">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-341">'Identity'</span></span>
- <span data-ttu-id="8c22f-342">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-343">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-343">'Razor'</span></span>
- <span data-ttu-id="8c22f-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-346">'Blazor'</span></span>
- <span data-ttu-id="8c22f-347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-347">'Identity'</span></span>
- <span data-ttu-id="8c22f-348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-349">'Razor'</span></span>
- <span data-ttu-id="8c22f-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-352">'Blazor'</span></span>
- <span data-ttu-id="8c22f-353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-353">'Identity'</span></span>
- <span data-ttu-id="8c22f-354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-355">'Razor'</span></span>
- <span data-ttu-id="8c22f-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-358">'Blazor'</span></span>
- <span data-ttu-id="8c22f-359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-359">'Identity'</span></span>
- <span data-ttu-id="8c22f-360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-361">'Razor'</span></span>
- <span data-ttu-id="8c22f-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-364">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-364">'Blazor'</span></span>
- <span data-ttu-id="8c22f-365">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-365">'Identity'</span></span>
- <span data-ttu-id="8c22f-366">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-367">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-367">'Razor'</span></span>
- <span data-ttu-id="8c22f-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-370">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-370">'Blazor'</span></span>
- <span data-ttu-id="8c22f-371">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-371">'Identity'</span></span>
- <span data-ttu-id="8c22f-372">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-373">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-373">'Razor'</span></span>
- <span data-ttu-id="8c22f-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-376">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-376">'Blazor'</span></span>
- <span data-ttu-id="8c22f-377">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-377">'Identity'</span></span>
- <span data-ttu-id="8c22f-378">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-379">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-379">'Razor'</span></span>
- <span data-ttu-id="8c22f-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-382">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-382">'Blazor'</span></span>
- <span data-ttu-id="8c22f-383">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-383">'Identity'</span></span>
- <span data-ttu-id="8c22f-384">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-385">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-385">'Razor'</span></span>
- <span data-ttu-id="8c22f-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-388">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-388">'Blazor'</span></span>
- <span data-ttu-id="8c22f-389">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-389">'Identity'</span></span>
- <span data-ttu-id="8c22f-390">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-391">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-391">'Razor'</span></span>
- <span data-ttu-id="8c22f-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-394">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-394">'Blazor'</span></span>
- <span data-ttu-id="8c22f-395">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-395">'Identity'</span></span>
- <span data-ttu-id="8c22f-396">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-397">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-397">'Razor'</span></span>
- <span data-ttu-id="8c22f-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-400">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-400">'Blazor'</span></span>
- <span data-ttu-id="8c22f-401">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-401">'Identity'</span></span>
- <span data-ttu-id="8c22f-402">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-403">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-403">'Razor'</span></span>
- <span data-ttu-id="8c22f-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-406">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-406">'Blazor'</span></span>
- <span data-ttu-id="8c22f-407">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-407">'Identity'</span></span>
- <span data-ttu-id="8c22f-408">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-409">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-409">'Razor'</span></span>
- <span data-ttu-id="8c22f-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-412">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-412">'Blazor'</span></span>
- <span data-ttu-id="8c22f-413">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-413">'Identity'</span></span>
- <span data-ttu-id="8c22f-414">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-415">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-415">'Razor'</span></span>
- <span data-ttu-id="8c22f-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-416">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sí   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span><span class="sxs-lookup"><span data-stu-id="8c22f-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="8c22f-418">Después de la parte `^rewrite-rule/` de la expresión, hay dos grupos de captura, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="8c22f-419">`\d` significa *buscar un dígito (número)* .</span><span class="sxs-lookup"><span data-stu-id="8c22f-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="8c22f-420">El signo más (`+`) significa *buscar uno o más de los caracteres anteriores*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="8c22f-421">Por tanto, la URL debe contener un número seguido de una barra diagonal, seguida de otro número.</span><span class="sxs-lookup"><span data-stu-id="8c22f-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="8c22f-422">Estos grupos de capturas se insertan en la URL de reescritura como `$1` y `$2`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="8c22f-423">La cadena de reemplazo de la regla de reescritura coloca los grupos capturados en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="8c22f-424">La ruta de acceso solicitada de `/rewrite-rule/1234/5678` se reescribe para obtener el recurso en `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="8c22f-425">Si una cadena de consulta está presente en la solicitud original, se conserva cuando se reescribe la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="8c22f-426">No hay ningún recorrido de ida y vuelta al servidor para obtener el recurso.</span><span class="sxs-lookup"><span data-stu-id="8c22f-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="8c22f-427">Si el recurso existe, se captura y se devuelve al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="8c22f-428">Como el cliente no se redirige, la dirección URL no cambia en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c22f-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="8c22f-429">Los clientes no pueden detectar que se ha producido una operación de reescritura de URL en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8c22f-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="8c22f-430">Use `skipRemainingRules: true` siempre que sea posible, ya que las reglas de coincidencia consumen muchos recursos y aumentan el tiempo de respuesta de aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="8c22f-431">Para obtener la respuesta más rápida de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8c22f-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="8c22f-432">Ordene las reglas de reescritura desde la que coincida con más frecuencia a la que coincida con menos frecuencia.</span><span class="sxs-lookup"><span data-stu-id="8c22f-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="8c22f-433">Omita el procesamiento de las reglas restantes cuando se produzca una coincidencia; no es necesario ningún procesamiento de reglas adicional.</span><span class="sxs-lookup"><span data-stu-id="8c22f-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="8c22f-434">mod_rewrite de Apache</span><span class="sxs-lookup"><span data-stu-id="8c22f-434">Apache mod_rewrite</span></span>

<span data-ttu-id="8c22f-435">Aplique reglas mod_rewrite de Apache con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="8c22f-436">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8c22f-437">Para obtener más información y ejemplos de reglas mod_rewrite, vea [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache).</span><span class="sxs-lookup"><span data-stu-id="8c22f-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="8c22f-438">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="8c22f-439">La aplicación de ejemplo redirige las solicitudes de `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="8c22f-440">El código de estado de la respuesta es *302: Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="8c22f-441">Solicitud original: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="8c22f-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="8c22f-443">El middleware admite las siguientes variables de servidor mod_rewrite de Apache:</span><span class="sxs-lookup"><span data-stu-id="8c22f-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="8c22f-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="8c22f-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8c22f-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8c22f-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8c22f-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8c22f-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8c22f-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="8c22f-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="8c22f-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="8c22f-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8c22f-449">HTTP_HOST</span></span>
* <span data-ttu-id="8c22f-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8c22f-450">HTTP_REFERER</span></span>
* <span data-ttu-id="8c22f-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8c22f-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8c22f-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8c22f-452">HTTPS</span></span>
* <span data-ttu-id="8c22f-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="8c22f-453">IPV6</span></span>
* <span data-ttu-id="8c22f-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8c22f-454">QUERY_STRING</span></span>
* <span data-ttu-id="8c22f-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="8c22f-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8c22f-456">REMOTE_PORT</span></span>
* <span data-ttu-id="8c22f-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8c22f-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8c22f-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="8c22f-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="8c22f-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="8c22f-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="8c22f-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8c22f-460">REQUEST_URI</span></span>
* <span data-ttu-id="8c22f-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8c22f-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="8c22f-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-462">SERVER_ADDR</span></span>
* <span data-ttu-id="8c22f-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="8c22f-463">SERVER_PORT</span></span>
* <span data-ttu-id="8c22f-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="8c22f-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="8c22f-465">TIME</span><span class="sxs-lookup"><span data-stu-id="8c22f-465">TIME</span></span>
* <span data-ttu-id="8c22f-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="8c22f-466">TIME_DAY</span></span>
* <span data-ttu-id="8c22f-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="8c22f-467">TIME_HOUR</span></span>
* <span data-ttu-id="8c22f-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="8c22f-468">TIME_MIN</span></span>
* <span data-ttu-id="8c22f-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="8c22f-469">TIME_MON</span></span>
* <span data-ttu-id="8c22f-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="8c22f-470">TIME_SEC</span></span>
* <span data-ttu-id="8c22f-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="8c22f-471">TIME_WDAY</span></span>
* <span data-ttu-id="8c22f-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="8c22f-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="8c22f-473">Reglas del Módulo URL Rewrite para IIS</span><span class="sxs-lookup"><span data-stu-id="8c22f-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="8c22f-474">Para usar el mismo conjunto de reglas que se aplica al módulo URL Rewrite para IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="8c22f-475">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8c22f-476">No dirija el middleware para que use el archivo *web.config* de la aplicación cuando se ejecute en Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="8c22f-477">Con IIS, estas reglas se deben almacenar fuera del archivo *web.config* de la aplicación para evitar conflictos con el Módulo URL Rewrite para IIS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="8c22f-478">Para obtener más información y ejemplos de reglas del Módulo URL Rewrite para IIS, vea [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0) y [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite).</span><span class="sxs-lookup"><span data-stu-id="8c22f-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="8c22f-479">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="8c22f-480">La aplicación de ejemplo reescribe las solicitudes de `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="8c22f-481">La respuesta se envía al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="8c22f-482">Solicitud original: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="8c22f-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="8c22f-484">Si tiene un Módulo URL Rewrite para IIS activo con reglas configuradas en el nivel de servidor que podrían afectar a la aplicación de manera no deseada, puede deshabilitar el Módulo URL Rewrite para IIS para una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="8c22f-485">Para más información, vea [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Deshabilitación de módulos de IIS).</span><span class="sxs-lookup"><span data-stu-id="8c22f-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="8c22f-486">Características no admitidas</span><span class="sxs-lookup"><span data-stu-id="8c22f-486">Unsupported features</span></span>

<span data-ttu-id="8c22f-487">El middleware publicado con ASP.NET Core 2.x no admite las siguientes características de Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="8c22f-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="8c22f-488">Reglas de salida</span><span class="sxs-lookup"><span data-stu-id="8c22f-488">Outbound Rules</span></span>
* <span data-ttu-id="8c22f-489">Variables de servidor personalizadas</span><span class="sxs-lookup"><span data-stu-id="8c22f-489">Custom Server Variables</span></span>
* <span data-ttu-id="8c22f-490">Caracteres comodín</span><span class="sxs-lookup"><span data-stu-id="8c22f-490">Wildcards</span></span>
* <span data-ttu-id="8c22f-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="8c22f-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="8c22f-492">Variables de servidor compatibles</span><span class="sxs-lookup"><span data-stu-id="8c22f-492">Supported server variables</span></span>

<span data-ttu-id="8c22f-493">El middleware admite las siguientes variables de servidor del Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="8c22f-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="8c22f-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="8c22f-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="8c22f-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="8c22f-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="8c22f-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8c22f-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8c22f-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8c22f-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8c22f-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8c22f-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="8c22f-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8c22f-499">HTTP_HOST</span></span>
* <span data-ttu-id="8c22f-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8c22f-500">HTTP_REFERER</span></span>
* <span data-ttu-id="8c22f-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-501">HTTP_URL</span></span>
* <span data-ttu-id="8c22f-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8c22f-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8c22f-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8c22f-503">HTTPS</span></span>
* <span data-ttu-id="8c22f-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="8c22f-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8c22f-505">QUERY_STRING</span></span>
* <span data-ttu-id="8c22f-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="8c22f-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8c22f-507">REMOTE_PORT</span></span>
* <span data-ttu-id="8c22f-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8c22f-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8c22f-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8c22f-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="8c22f-510">También puede obtener <xref:Microsoft.Extensions.FileProviders.IFileProvider> a través de <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="8c22f-511">Con este enfoque logrará mayor flexibilidad para la ubicación de los archivos de reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="8c22f-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="8c22f-512">Asegúrese de que los archivos de reglas de reescritura se implementan en el servidor en la ruta de acceso que proporcione.</span><span class="sxs-lookup"><span data-stu-id="8c22f-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="8c22f-513">Regla basada en métodos</span><span class="sxs-lookup"><span data-stu-id="8c22f-513">Method-based rule</span></span>

<span data-ttu-id="8c22f-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar su propia lógica de la regla en un método.</span><span class="sxs-lookup"><span data-stu-id="8c22f-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="8c22f-515">`Add` expone el elemento <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, lo que hace que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible para usarlo en el método.</span><span class="sxs-lookup"><span data-stu-id="8c22f-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="8c22f-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina cómo se administra el procesamiento adicional en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8c22f-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="8c22f-517">Establezca el valor en uno de los campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> que se describen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="8c22f-518">Acción</span><span class="sxs-lookup"><span data-stu-id="8c22f-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="8c22f-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-520">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-520">'Blazor'</span></span>
- <span data-ttu-id="8c22f-521">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-521">'Identity'</span></span>
- <span data-ttu-id="8c22f-522">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-523">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-523">'Razor'</span></span>
- <span data-ttu-id="8c22f-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-526">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-526">'Blazor'</span></span>
- <span data-ttu-id="8c22f-527">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-527">'Identity'</span></span>
- <span data-ttu-id="8c22f-528">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-529">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-529">'Razor'</span></span>
- <span data-ttu-id="8c22f-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-532">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-532">'Blazor'</span></span>
- <span data-ttu-id="8c22f-533">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-533">'Identity'</span></span>
- <span data-ttu-id="8c22f-534">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-535">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-535">'Razor'</span></span>
- <span data-ttu-id="8c22f-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-538">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-538">'Blazor'</span></span>
- <span data-ttu-id="8c22f-539">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-539">'Identity'</span></span>
- <span data-ttu-id="8c22f-540">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-541">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-541">'Razor'</span></span>
- <span data-ttu-id="8c22f-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-544">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-544">'Blazor'</span></span>
- <span data-ttu-id="8c22f-545">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-545">'Identity'</span></span>
- <span data-ttu-id="8c22f-546">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-547">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-547">'Razor'</span></span>
- <span data-ttu-id="8c22f-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-550">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-550">'Blazor'</span></span>
- <span data-ttu-id="8c22f-551">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-551">'Identity'</span></span>
- <span data-ttu-id="8c22f-552">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-553">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-553">'Razor'</span></span>
- <span data-ttu-id="8c22f-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-556">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-556">'Blazor'</span></span>
- <span data-ttu-id="8c22f-557">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-557">'Identity'</span></span>
- <span data-ttu-id="8c22f-558">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-559">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-559">'Razor'</span></span>
- <span data-ttu-id="8c22f-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-562">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-562">'Blazor'</span></span>
- <span data-ttu-id="8c22f-563">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-563">'Identity'</span></span>
- <span data-ttu-id="8c22f-564">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-565">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-565">'Razor'</span></span>
- <span data-ttu-id="8c22f-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-568">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-568">'Blazor'</span></span>
- <span data-ttu-id="8c22f-569">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-569">'Identity'</span></span>
- <span data-ttu-id="8c22f-570">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-571">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-571">'Razor'</span></span>
- <span data-ttu-id="8c22f-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-574">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-574">'Blazor'</span></span>
- <span data-ttu-id="8c22f-575">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-575">'Identity'</span></span>
- <span data-ttu-id="8c22f-576">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-577">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-577">'Razor'</span></span>
- <span data-ttu-id="8c22f-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-580">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-580">'Blazor'</span></span>
- <span data-ttu-id="8c22f-581">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-581">'Identity'</span></span>
- <span data-ttu-id="8c22f-582">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-583">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-583">'Razor'</span></span>
- <span data-ttu-id="8c22f-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-586">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-586">'Blazor'</span></span>
- <span data-ttu-id="8c22f-587">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-587">'Identity'</span></span>
- <span data-ttu-id="8c22f-588">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-589">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-589">'Razor'</span></span>
- <span data-ttu-id="8c22f-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-592">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-592">'Blazor'</span></span>
- <span data-ttu-id="8c22f-593">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-593">'Identity'</span></span>
- <span data-ttu-id="8c22f-594">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-595">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-595">'Razor'</span></span>
- <span data-ttu-id="8c22f-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-598">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-598">'Blazor'</span></span>
- <span data-ttu-id="8c22f-599">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-599">'Identity'</span></span>
- <span data-ttu-id="8c22f-600">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-601">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-601">'Razor'</span></span>
- <span data-ttu-id="8c22f-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-604">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-604">'Blazor'</span></span>
- <span data-ttu-id="8c22f-605">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-605">'Identity'</span></span>
- <span data-ttu-id="8c22f-606">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-607">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-607">'Razor'</span></span>
- <span data-ttu-id="8c22f-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-610">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-610">'Blazor'</span></span>
- <span data-ttu-id="8c22f-611">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-611">'Identity'</span></span>
- <span data-ttu-id="8c22f-612">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-613">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-613">'Razor'</span></span>
- <span data-ttu-id="8c22f-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-614">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-616">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-616">'Blazor'</span></span>
- <span data-ttu-id="8c22f-617">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-617">'Identity'</span></span>
- <span data-ttu-id="8c22f-618">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-619">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-619">'Razor'</span></span>
- <span data-ttu-id="8c22f-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-622">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-622">'Blazor'</span></span>
- <span data-ttu-id="8c22f-623">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-623">'Identity'</span></span>
- <span data-ttu-id="8c22f-624">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-625">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-625">'Razor'</span></span>
- <span data-ttu-id="8c22f-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-628">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-628">'Blazor'</span></span>
- <span data-ttu-id="8c22f-629">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-629">'Identity'</span></span>
- <span data-ttu-id="8c22f-630">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-631">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-631">'Razor'</span></span>
- <span data-ttu-id="8c22f-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-634">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-634">'Blazor'</span></span>
- <span data-ttu-id="8c22f-635">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-635">'Identity'</span></span>
- <span data-ttu-id="8c22f-636">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-637">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-637">'Razor'</span></span>
- <span data-ttu-id="8c22f-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-640">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-640">'Blazor'</span></span>
- <span data-ttu-id="8c22f-641">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-641">'Identity'</span></span>
- <span data-ttu-id="8c22f-642">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-643">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-643">'Razor'</span></span>
- <span data-ttu-id="8c22f-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-646">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-646">'Blazor'</span></span>
- <span data-ttu-id="8c22f-647">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-647">'Identity'</span></span>
- <span data-ttu-id="8c22f-648">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-649">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-649">'Razor'</span></span>
- <span data-ttu-id="8c22f-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-652">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-652">'Blazor'</span></span>
- <span data-ttu-id="8c22f-653">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-653">'Identity'</span></span>
- <span data-ttu-id="8c22f-654">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-655">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-655">'Razor'</span></span>
- <span data-ttu-id="8c22f-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-658">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-658">'Blazor'</span></span>
- <span data-ttu-id="8c22f-659">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-659">'Identity'</span></span>
- <span data-ttu-id="8c22f-660">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-661">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-661">'Razor'</span></span>
- <span data-ttu-id="8c22f-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-664">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-664">'Blazor'</span></span>
- <span data-ttu-id="8c22f-665">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-665">'Identity'</span></span>
- <span data-ttu-id="8c22f-666">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-667">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-667">'Razor'</span></span>
- <span data-ttu-id="8c22f-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-670">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-670">'Blazor'</span></span>
- <span data-ttu-id="8c22f-671">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-671">'Identity'</span></span>
- <span data-ttu-id="8c22f-672">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-673">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-673">'Razor'</span></span>
- <span data-ttu-id="8c22f-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-676">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-676">'Blazor'</span></span>
- <span data-ttu-id="8c22f-677">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-677">'Identity'</span></span>
- <span data-ttu-id="8c22f-678">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-679">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-679">'Razor'</span></span>
- <span data-ttu-id="8c22f-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-682">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-682">'Blazor'</span></span>
- <span data-ttu-id="8c22f-683">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-683">'Identity'</span></span>
- <span data-ttu-id="8c22f-684">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-685">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-685">'Razor'</span></span>
- <span data-ttu-id="8c22f-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-688">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-688">'Blazor'</span></span>
- <span data-ttu-id="8c22f-689">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-689">'Identity'</span></span>
- <span data-ttu-id="8c22f-690">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-691">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-691">'Razor'</span></span>
- <span data-ttu-id="8c22f-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-694">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-694">'Blazor'</span></span>
- <span data-ttu-id="8c22f-695">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-695">'Identity'</span></span>
- <span data-ttu-id="8c22f-696">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-697">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-697">'Razor'</span></span>
- <span data-ttu-id="8c22f-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-700">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-700">'Blazor'</span></span>
- <span data-ttu-id="8c22f-701">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-701">'Identity'</span></span>
- <span data-ttu-id="8c22f-702">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-703">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-703">'Razor'</span></span>
- <span data-ttu-id="8c22f-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-706">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-706">'Blazor'</span></span>
- <span data-ttu-id="8c22f-707">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-707">'Identity'</span></span>
- <span data-ttu-id="8c22f-708">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-709">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-709">'Razor'</span></span>
- <span data-ttu-id="8c22f-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-712">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-712">'Blazor'</span></span>
- <span data-ttu-id="8c22f-713">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-713">'Identity'</span></span>
- <span data-ttu-id="8c22f-714">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-715">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-715">'Razor'</span></span>
- <span data-ttu-id="8c22f-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-718">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-718">'Blazor'</span></span>
- <span data-ttu-id="8c22f-719">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-719">'Identity'</span></span>
- <span data-ttu-id="8c22f-720">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-721">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-721">'Razor'</span></span>
- <span data-ttu-id="8c22f-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-724">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-724">'Blazor'</span></span>
- <span data-ttu-id="8c22f-725">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-725">'Identity'</span></span>
- <span data-ttu-id="8c22f-726">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-727">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-727">'Razor'</span></span>
- <span data-ttu-id="8c22f-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-730">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-730">'Blazor'</span></span>
- <span data-ttu-id="8c22f-731">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-731">'Identity'</span></span>
- <span data-ttu-id="8c22f-732">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-733">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-733">'Razor'</span></span>
- <span data-ttu-id="8c22f-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-736">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-736">'Blazor'</span></span>
- <span data-ttu-id="8c22f-737">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-737">'Identity'</span></span>
- <span data-ttu-id="8c22f-738">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-739">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-739">'Razor'</span></span>
- <span data-ttu-id="8c22f-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-742">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-742">'Blazor'</span></span>
- <span data-ttu-id="8c22f-743">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-743">'Identity'</span></span>
- <span data-ttu-id="8c22f-744">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-745">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-745">'Razor'</span></span>
- <span data-ttu-id="8c22f-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-748">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-748">'Blazor'</span></span>
- <span data-ttu-id="8c22f-749">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-749">'Identity'</span></span>
- <span data-ttu-id="8c22f-750">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-751">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-751">'Razor'</span></span>
- <span data-ttu-id="8c22f-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-754">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-754">'Blazor'</span></span>
- <span data-ttu-id="8c22f-755">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-755">'Identity'</span></span>
- <span data-ttu-id="8c22f-756">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-757">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-757">'Razor'</span></span>
- <span data-ttu-id="8c22f-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-760">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-760">'Blazor'</span></span>
- <span data-ttu-id="8c22f-761">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-761">'Identity'</span></span>
- <span data-ttu-id="8c22f-762">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-763">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-763">'Razor'</span></span>
- <span data-ttu-id="8c22f-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-766">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-766">'Blazor'</span></span>
- <span data-ttu-id="8c22f-767">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-767">'Identity'</span></span>
- <span data-ttu-id="8c22f-768">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-769">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-769">'Razor'</span></span>
- <span data-ttu-id="8c22f-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-772">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-772">'Blazor'</span></span>
- <span data-ttu-id="8c22f-773">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-773">'Identity'</span></span>
- <span data-ttu-id="8c22f-774">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-775">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-775">'Razor'</span></span>
- <span data-ttu-id="8c22f-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-778">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-778">'Blazor'</span></span>
- <span data-ttu-id="8c22f-779">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-779">'Identity'</span></span>
- <span data-ttu-id="8c22f-780">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-781">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-781">'Razor'</span></span>
- <span data-ttu-id="8c22f-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-784">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-784">'Blazor'</span></span>
- <span data-ttu-id="8c22f-785">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-785">'Identity'</span></span>
- <span data-ttu-id="8c22f-786">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-787">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-787">'Razor'</span></span>
- <span data-ttu-id="8c22f-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-790">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-790">'Blazor'</span></span>
- <span data-ttu-id="8c22f-791">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-791">'Identity'</span></span>
- <span data-ttu-id="8c22f-792">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-793">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-793">'Razor'</span></span>
- <span data-ttu-id="8c22f-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-794">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-795">-------------------------------- | | `RuleResult.ContinueRules` (valor predeterminado) | Continuar aplicando las reglas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="8c22f-796">| | `RuleResult.EndResponse`             | Dejar de aplicar las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="8c22f-797">| | `RuleResult.SkipRemainingRules`      | Dejar de aplicar las reglas y enviar el contexto al middleware siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="8c22f-798">La aplicación de ejemplo muestra un método que redirige las solicitudes para las rutas de acceso que terminen con *.xml*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="8c22f-799">Si se realiza una solicitud de `/file.xml`, la solicitud se redirige a `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="8c22f-800">El código de estado se establece en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="8c22f-801">Cuando el explorador realiza una solicitud nueva a */xmlfiles/file.xml*, el middleware de archivos estáticos sirve el archivo al cliente desde la carpeta *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="8c22f-802">Para un redireccionamiento, debe establecer de forma explícita el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="8c22f-803">En caso contrario, se devuelve un código de estado *200: correcto* y no se produce el redireccionamiento en el cliente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="8c22f-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="8c22f-805">Este enfoque también permite volver a escribir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="8c22f-806">En la aplicación de ejemplo se muestra cómo volver a escribir la ruta de acceso para cualquier solicitud de archivo de texto para proporcionar el archivo de texto *file.txt* desde la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="8c22f-807">El middleware de archivos estáticos proporciona el archivo en función de la ruta de acceso de la solicitud actualizada:</span><span class="sxs-lookup"><span data-stu-id="8c22f-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="8c22f-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="8c22f-809">Regla basada en IRule</span><span class="sxs-lookup"><span data-stu-id="8c22f-809">IRule-based rule</span></span>

<span data-ttu-id="8c22f-810">Utilice <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar la lógica de reglas en una clase que implemente la interfaz <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="8c22f-811">`IRule` proporciona mayor flexibilidad que si se usa el enfoque de reglas basadas en métodos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="8c22f-812">La clase de implementación puede incluir un constructor que permita pasar parámetros para el método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="8c22f-813">Se comprueba que los valores de los parámetros en la aplicación de ejemplo para `extension` y `newPath` cumplen ciertas condiciones.</span><span class="sxs-lookup"><span data-stu-id="8c22f-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="8c22f-814">`extension` debe contener un valor, que debe ser *.png*, *.jpg* o *.gif*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="8c22f-815">Si `newPath` no es válido, se genera <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="8c22f-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="8c22f-816">Si se realiza una solicitud de *image.png*, la solicitud se redirige a `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="8c22f-817">Si se realiza una solicitud de *image.jpg*, la solicitud se redirige a `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="8c22f-818">El código de estado se establece en *301 - Movido definitivamente* y se establece `context.Result` para detener el procesamiento de las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="8c22f-819">Solicitud original: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="8c22f-819">Original Request: `/image.png`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="8c22f-821">Solicitud original: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="8c22f-821">Original Request: `/image.jpg`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="8c22f-823">Ejemplos de expresiones regulares</span><span class="sxs-lookup"><span data-stu-id="8c22f-823">Regex examples</span></span>

| <span data-ttu-id="8c22f-824">Objetivo</span><span class="sxs-lookup"><span data-stu-id="8c22f-824">Goal</span></span> | <span data-ttu-id="8c22f-825">Cadena de expresión regular &</span><span class="sxs-lookup"><span data-stu-id="8c22f-825">Regex String &</span></span><br><span data-ttu-id="8c22f-826">Ejemplo de coincidencia</span><span class="sxs-lookup"><span data-stu-id="8c22f-826">Match Example</span></span> | <span data-ttu-id="8c22f-827">Cadena de reemplazo &</span><span class="sxs-lookup"><span data-stu-id="8c22f-827">Replacement String &</span></span><br><span data-ttu-id="8c22f-828">Ejemplo de resultado</span><span class="sxs-lookup"><span data-stu-id="8c22f-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="8c22f-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-830">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-830">'Blazor'</span></span>
- <span data-ttu-id="8c22f-831">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-831">'Identity'</span></span>
- <span data-ttu-id="8c22f-832">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-833">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-833">'Razor'</span></span>
- <span data-ttu-id="8c22f-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-836">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-836">'Blazor'</span></span>
- <span data-ttu-id="8c22f-837">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-837">'Identity'</span></span>
- <span data-ttu-id="8c22f-838">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-839">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-839">'Razor'</span></span>
- <span data-ttu-id="8c22f-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-842">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-842">'Blazor'</span></span>
- <span data-ttu-id="8c22f-843">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-843">'Identity'</span></span>
- <span data-ttu-id="8c22f-844">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-845">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-845">'Razor'</span></span>
- <span data-ttu-id="8c22f-846">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-848">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-848">'Blazor'</span></span>
- <span data-ttu-id="8c22f-849">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-849">'Identity'</span></span>
- <span data-ttu-id="8c22f-850">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-851">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-851">'Razor'</span></span>
- <span data-ttu-id="8c22f-852">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-854">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-854">'Blazor'</span></span>
- <span data-ttu-id="8c22f-855">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-855">'Identity'</span></span>
- <span data-ttu-id="8c22f-856">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-857">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-857">'Razor'</span></span>
- <span data-ttu-id="8c22f-858">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-860">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-860">'Blazor'</span></span>
- <span data-ttu-id="8c22f-861">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-861">'Identity'</span></span>
- <span data-ttu-id="8c22f-862">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-863">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-863">'Razor'</span></span>
- <span data-ttu-id="8c22f-864">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-866">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-866">'Blazor'</span></span>
- <span data-ttu-id="8c22f-867">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-867">'Identity'</span></span>
- <span data-ttu-id="8c22f-868">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-869">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-869">'Razor'</span></span>
- <span data-ttu-id="8c22f-870">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-872">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-872">'Blazor'</span></span>
- <span data-ttu-id="8c22f-873">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-873">'Identity'</span></span>
- <span data-ttu-id="8c22f-874">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-875">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-875">'Razor'</span></span>
- <span data-ttu-id="8c22f-876">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-878">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-878">'Blazor'</span></span>
- <span data-ttu-id="8c22f-879">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-879">'Identity'</span></span>
- <span data-ttu-id="8c22f-880">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-881">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-881">'Razor'</span></span>
- <span data-ttu-id="8c22f-882">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-884">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-884">'Blazor'</span></span>
- <span data-ttu-id="8c22f-885">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-885">'Identity'</span></span>
- <span data-ttu-id="8c22f-886">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-887">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-887">'Razor'</span></span>
- <span data-ttu-id="8c22f-888">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-890">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-890">'Blazor'</span></span>
- <span data-ttu-id="8c22f-891">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-891">'Identity'</span></span>
- <span data-ttu-id="8c22f-892">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-893">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-893">'Razor'</span></span>
- <span data-ttu-id="8c22f-894">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-896">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-896">'Blazor'</span></span>
- <span data-ttu-id="8c22f-897">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-897">'Identity'</span></span>
- <span data-ttu-id="8c22f-898">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-899">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-899">'Razor'</span></span>
- <span data-ttu-id="8c22f-900">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-902">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-902">'Blazor'</span></span>
- <span data-ttu-id="8c22f-903">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-903">'Identity'</span></span>
- <span data-ttu-id="8c22f-904">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-905">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-905">'Razor'</span></span>
- <span data-ttu-id="8c22f-906">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-906">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-908">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-908">'Blazor'</span></span>
- <span data-ttu-id="8c22f-909">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-909">'Identity'</span></span>
- <span data-ttu-id="8c22f-910">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-911">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-911">'Razor'</span></span>
- <span data-ttu-id="8c22f-912">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-914">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-914">'Blazor'</span></span>
- <span data-ttu-id="8c22f-915">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-915">'Identity'</span></span>
- <span data-ttu-id="8c22f-916">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-917">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-917">'Razor'</span></span>
- <span data-ttu-id="8c22f-918">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-920">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-920">'Blazor'</span></span>
- <span data-ttu-id="8c22f-921">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-921">'Identity'</span></span>
- <span data-ttu-id="8c22f-922">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-923">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-923">'Razor'</span></span>
- <span data-ttu-id="8c22f-924">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-926">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-926">'Blazor'</span></span>
- <span data-ttu-id="8c22f-927">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-927">'Identity'</span></span>
- <span data-ttu-id="8c22f-928">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-929">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-929">'Razor'</span></span>
- <span data-ttu-id="8c22f-930">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-932">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-932">'Blazor'</span></span>
- <span data-ttu-id="8c22f-933">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-933">'Identity'</span></span>
- <span data-ttu-id="8c22f-934">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-935">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-935">'Razor'</span></span>
- <span data-ttu-id="8c22f-936">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-938">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-938">'Blazor'</span></span>
- <span data-ttu-id="8c22f-939">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-939">'Identity'</span></span>
- <span data-ttu-id="8c22f-940">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-941">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-941">'Razor'</span></span>
- <span data-ttu-id="8c22f-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-944">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-944">'Blazor'</span></span>
- <span data-ttu-id="8c22f-945">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-945">'Identity'</span></span>
- <span data-ttu-id="8c22f-946">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-947">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-947">'Razor'</span></span>
- <span data-ttu-id="8c22f-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-950">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-950">'Blazor'</span></span>
- <span data-ttu-id="8c22f-951">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-951">'Identity'</span></span>
- <span data-ttu-id="8c22f-952">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-953">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-953">'Razor'</span></span>
- <span data-ttu-id="8c22f-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-956">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-956">'Blazor'</span></span>
- <span data-ttu-id="8c22f-957">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-957">'Identity'</span></span>
- <span data-ttu-id="8c22f-958">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-959">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-959">'Razor'</span></span>
- <span data-ttu-id="8c22f-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-962">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-962">'Blazor'</span></span>
- <span data-ttu-id="8c22f-963">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-963">'Identity'</span></span>
- <span data-ttu-id="8c22f-964">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-965">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-965">'Razor'</span></span>
- <span data-ttu-id="8c22f-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-968">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-968">'Blazor'</span></span>
- <span data-ttu-id="8c22f-969">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-969">'Identity'</span></span>
- <span data-ttu-id="8c22f-970">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-971">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-971">'Razor'</span></span>
- <span data-ttu-id="8c22f-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-974">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-974">'Blazor'</span></span>
- <span data-ttu-id="8c22f-975">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-975">'Identity'</span></span>
- <span data-ttu-id="8c22f-976">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-977">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-977">'Razor'</span></span>
- <span data-ttu-id="8c22f-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-980">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-980">'Blazor'</span></span>
- <span data-ttu-id="8c22f-981">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-981">'Identity'</span></span>
- <span data-ttu-id="8c22f-982">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-983">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-983">'Razor'</span></span>
- <span data-ttu-id="8c22f-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-986">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-986">'Blazor'</span></span>
- <span data-ttu-id="8c22f-987">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-987">'Identity'</span></span>
- <span data-ttu-id="8c22f-988">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-989">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-989">'Razor'</span></span>
- <span data-ttu-id="8c22f-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-992">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-992">'Blazor'</span></span>
- <span data-ttu-id="8c22f-993">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-993">'Identity'</span></span>
- <span data-ttu-id="8c22f-994">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-995">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-995">'Razor'</span></span>
- <span data-ttu-id="8c22f-996">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-998">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-998">'Blazor'</span></span>
- <span data-ttu-id="8c22f-999">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-999">'Identity'</span></span>
- <span data-ttu-id="8c22f-1000">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1001">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1001">'Razor'</span></span>
- <span data-ttu-id="8c22f-1002">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1004">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1004">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1005">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1005">'Identity'</span></span>
- <span data-ttu-id="8c22f-1006">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1007">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1007">'Razor'</span></span>
- <span data-ttu-id="8c22f-1008">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1009">------------------- | | Ruta de acceso de reescritura en la cadena de consulta | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="8c22f-1010">`/path?var1=abc&var2=123` | | Quitar barra diagonal final | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="8c22f-1011">`/path` | | Exigir barra diagonal final | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="8c22f-1012">`/path/` | | Evitar reescritura de solicitudes específicas | `^(.*)(?<!\.axd)$` o `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="8c22f-1013">Sí: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="8c22f-1014">No: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="8c22f-1015">`/resource.axd` | | Reorganizar los segmentos de URL | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="8c22f-1016">`path/3/2/1` | | Reemplazar un segmento de URL | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c22f-1017">En este documento se ofrece una introducción a la reescritura de URL e instrucciones sobre cómo usar el middleware de reescritura de URL en aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="8c22f-1018">La reescritura de URL consiste en modificar varias URL de solicitud basadas en una o varias reglas predefinidas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="8c22f-1019">La reescritura de URL crea una abstracción entre las ubicaciones de recursos y sus direcciones para que las ubicaciones y las direcciones no estén estrechamente vinculadas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="8c22f-1020">La reescritura de URL es útil en varios escenarios para:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="8c22f-1021">Mover o reemplazar recursos del servidor de forma temporal o permanente a la vez que se mantienen localizadores estables para esos recursos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="8c22f-1022">Dividir el procesamiento de solicitudes entre otras aplicaciones o entre áreas de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="8c22f-1023">Quitar, agregar o reorganizar segmentos de dirección URL en solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="8c22f-1024">Optimizar direcciones URL públicas para la optimización del motor de búsqueda (SEO).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="8c22f-1025">Permitir el uso de direcciones URL públicas descriptivas para ayudar a los visitantes a predecir el contenido devuelto mediante la solicitud de un recurso.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="8c22f-1026">Redirigir solicitudes no seguras a puntos de conexión seguros.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="8c22f-1027">Evitar la vinculación activa, en la que un sitio externo usa un recurso estático hospedado en otro sitio mediante la vinculación del recurso a su propio contenido.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="8c22f-1028">La reescritura de URL puede reducir el rendimiento de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="8c22f-1029">Cuando sea factible, limite el número y la complejidad de las reglas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="8c22f-1030">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8c22f-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="8c22f-1031">Redireccionamiento y reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="8c22f-1032">La diferencia entre los términos *redirección de URL* y *reescritura de URL* es sutil, pero tiene importantes implicaciones para proporcionar recursos a los clientes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="8c22f-1033">El middleware de reescritura de URL de ASP.NET Core es capaz de satisfacer las necesidades de ambos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="8c22f-1034">Una *redirección de URL* implica una operación del lado cliente, donde al cliente se le indica que acceda a un recurso en una dirección distinta a la que ha solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="8c22f-1035">Esto requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="8c22f-1036">La URL de redireccionamiento que se devuelve al cliente aparece en la barra de direcciones del explorador cuando el cliente realiza una nueva solicitud para el recurso.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="8c22f-1037">Si `/resource` se *redirige* a `/different-resource`, el servidor responde que el cliente debe obtener el recurso en `/different-resource` con un código de estado que indica que la redirección es temporal o permanente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Se cambió temporalmente un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="8c22f-1043">Al redirigir las solicitudes a otra dirección URL, se indica si la redirección es permanente o temporal mediante la especificación del código de estado con la respuesta:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="8c22f-1044">El código de estado *301 - Movido definitivamente* se usa cuando el recurso tiene una nueva dirección URL permanente y se quiere indicar al cliente que todas las solicitudes futuras para el recurso deben usar la nueva dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="8c22f-1045">*El cliente puede almacenar en caché la respuesta cuando se recibe un código de estado 301.*</span><span class="sxs-lookup"><span data-stu-id="8c22f-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="8c22f-1046">El código de estado *302: encontrado* se usa cuando el redireccionamiento es temporal o en general está sujeto a cambios.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="8c22f-1047">El código de estado 302 indica al cliente que no almacene la dirección URL y la use en el futuro.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="8c22f-1048">Para obtener más información sobre los códigos de estado, consulte [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: definiciones de los códigos de estado).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="8c22f-1049">La *reescritura de URL* es una operación del lado servidor que proporciona un recurso desde una dirección de recursos distinta a la que el cliente ha solicitado.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="8c22f-1050">La reescritura de una dirección URL no requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="8c22f-1051">La dirección URL reescrita no se devuelve al cliente y no aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="8c22f-1052">Si `/resource` se *reescribe* como `/different-resource`, el servidor la obtiene *internamente* y devuelve el recurso en `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="8c22f-1053">Aunque es posible que el cliente pueda recuperar el recurso en la dirección URL reescrita, no se le informa de que el recurso existe en la dirección URL reescrita cuando realiza su solicitud y recibe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Se cambió un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="8c22f-1058">Aplicación de ejemplo de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1058">URL rewriting sample app</span></span>

<span data-ttu-id="8c22f-1059">Puede explorar las características del middleware de reescritura de URL con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="8c22f-1060">La aplicación emplea las reglas de redireccionamiento y reescritura, y muestra la URL redirigida o reescrita para varios escenarios.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="8c22f-1061">Cuándo usar el middleware de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="8c22f-1062">Use el middleware de reescritura de URL cuando no pueda usar los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="8c22f-1063">Módulo de reescritura de URL con IIS en Windows Server</span><span class="sxs-lookup"><span data-stu-id="8c22f-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="8c22f-1064">Módulo mod_rewrite de Apache en el servidor Apache</span><span class="sxs-lookup"><span data-stu-id="8c22f-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="8c22f-1065">Reescritura de URL en Nginx</span><span class="sxs-lookup"><span data-stu-id="8c22f-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="8c22f-1066">Además, use el middleware cuando la aplicación se hospede en el [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="8c22f-1067">Las principales razones para usar la tecnologías de reescritura de URL basadas en servidor en IIS, Apache y Nginx son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="8c22f-1068">El middleware no es compatible con todas las características de estos módulos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="8c22f-1069">Algunas de las características de los módulos de servidor no funcionan con proyectos de ASP.NET Core, como las restricciones `IsFile` y `IsDirectory` del módulo de reescritura de IIS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="8c22f-1070">En estos casos, es mejor usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="8c22f-1071">El rendimiento del middleware probablemente no coincida con el de los módulos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="8c22f-1072">La única manera de saber con certeza con qué enfoque el rendimiento disminuye más, o si la disminución de este es insignificante, es mediante una comparación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="8c22f-1073">Package</span><span class="sxs-lookup"><span data-stu-id="8c22f-1073">Package</span></span>

<span data-ttu-id="8c22f-1074">Para incluir el middleware en el proyecto, agregue una referencia de paquete al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) del archivo de proyecto, que contiene el paquete [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="8c22f-1075">Si no se usa el metapaquete `Microsoft.AspNetCore.App`, agregue una referencia de proyecto al paquete `Microsoft.AspNetCore.Rewrite`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="8c22f-1076">Extensión y opciones</span><span class="sxs-lookup"><span data-stu-id="8c22f-1076">Extension and options</span></span>

<span data-ttu-id="8c22f-1077">Establezca las reglas de reescritura y redirección de URL mediante la creación de una instancia de la clase [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con métodos de extensión para cada una de las reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="8c22f-1078">Encadene varias reglas en el orden que quiera procesarlas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="8c22f-1079">Las `RewriteOptions` se pasan al middleware de reescritura de URL cuando se agrega a la canalización de solicitudes con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="8c22f-1080">Redirigir solicitudes que distintas de www a www</span><span class="sxs-lookup"><span data-stu-id="8c22f-1080">Redirect non-www to www</span></span>

<span data-ttu-id="8c22f-1081">Hay tres opciones que permiten a la aplicación redirigir solicitudes distintas de `www` a `www`:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="8c22f-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: redirige permanentemente la solicitud al subdominio `www` si la solicitud es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="8c22f-1083">Se redirige con un código de estado [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="8c22f-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: redirige la solicitud al subdominio `www` si la solicitud de entrada es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="8c22f-1085">Se redirige con un código de estado [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="8c22f-1086">Una sobrecarga permite proporcionar el código de estado para la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="8c22f-1087">Use un campo de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para una asignación de código de estado.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="8c22f-1088">Redirección de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1088">URL redirect</span></span>

<span data-ttu-id="8c22f-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirigir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="8c22f-1090">El primer parámetro contiene la expresión regular para hacer coincidir la ruta de acceso de la URL entrante.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="8c22f-1091">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="8c22f-1092">El tercer parámetro, si está presente, especifica el código de estado.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="8c22f-1093">Si no se especifica el código de estado, el valor predeterminado es *302 - Encontrado*, lo que indica que el recurso se ha movido o reemplazado temporalmente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="8c22f-1094">En un explorador con herramientas de desarrollo habilitadas, realice una solicitud a la aplicación de ejemplo con la ruta de acceso `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="8c22f-1095">La expresión regular coincide con la ruta de acceso de la solicitud en `redirect-rule/(.*)` y la ruta de acceso se reemplaza con `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="8c22f-1096">La dirección URL de redireccionamiento se devuelve al cliente con un código de estado *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="8c22f-1097">El explorador realiza una solicitud nueva en la URL de redireccionamiento, que aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="8c22f-1098">Como ninguna de las reglas de la aplicación de ejemplo coincide con la dirección URL de redireccionamiento:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="8c22f-1099">La segunda solicitud recibe una respuesta *200: correcto* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="8c22f-1100">En el cuerpo de la respuesta se muestra la dirección URL de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="8c22f-1101">Se realiza un recorrido de ida y vuelta al servidor cuando se *redirige* una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="8c22f-1102">Tenga cuidado al establecer las reglas de redirección.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="8c22f-1103">Las reglas de redirección se evalúan en cada solicitud enviada a la aplicación, incluso después de una redirección.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="8c22f-1104">Es fácil crear por error un *bucle de redirecciones infinitas*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="8c22f-1105">Solicitud original: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="8c22f-1107">La parte de la expresión incluida entre paréntesis se denomina *grupo de capturas*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="8c22f-1108">El punto (`.`) de la expresión significa *buscar cualquier carácter*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="8c22f-1109">El asterisco (`*`) indica *buscar el carácter precedente cero o más veces*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="8c22f-1110">Por tanto, el grupo de capturas `(.*)` busca los dos últimos segmentos de la ruta de acceso de la URL, `1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="8c22f-1111">Cualquier valor que se proporcione en la URL de la solicitud después de `redirect-rule/` es capturado por este grupo de capturas único.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="8c22f-1112">En la cadena de reemplazo, se insertan los grupos capturados en la cadena con el signo de dólar (`$`) seguido del número de secuencia de la captura.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="8c22f-1113">Se obtiene el valor del primer grupo de capturas con `$1`, el segundo con `$2`, y así siguen en secuencia para los grupos de capturas de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="8c22f-1114">Solo hay un grupo capturado en la expresión regular de la regla de redirección de la aplicación de ejemplo, por lo que solo hay un grupo insertado en la cadena de reemplazo, que es `$1`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="8c22f-1115">Cuando se aplica la regla, la URL se convierte en `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="8c22f-1116">Redirección de URL a un punto de conexión segura</span><span class="sxs-lookup"><span data-stu-id="8c22f-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="8c22f-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirigir las solicitudes HTTP al mismo host y ruta de acceso mediante el protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="8c22f-1118">Si no se proporciona el código de estado, el middleware muestra de forma predeterminada *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="8c22f-1119">Si no se proporciona el puerto:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="8c22f-1120">El middleware tiene como valor predeterminado `null`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="8c22f-1121">El esquema cambia a `https` (protocolo HTTPS), y el cliente accede al recurso en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="8c22f-1122">En el ejemplo siguiente se muestra cómo establecer el código de estado en *301 - Movido definitivamente* y cambiar el puerto a 5001.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="8c22f-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirigir las solicitudes no seguras al mismo host y ruta de acceso mediante el protocolo HTTPS seguro en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="8c22f-1124">El middleware establece el código de estado en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="8c22f-1125">Al redirigir a un punto de conexión seguro sin la necesidad de reglas de redirección adicionales, se recomienda usar el Middleware de redirección de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="8c22f-1126">Para más información, vea el tema [Aplicación de HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="8c22f-1127">La aplicación de ejemplo es capaz de mostrar cómo usar `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="8c22f-1128">Agregue el método de extensión a `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="8c22f-1129">Realice una solicitud poco segura a la aplicación en cualquier URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="8c22f-1130">Descarte la advertencia de seguridad del explorador que indica que el certificado autofirmado no es de confianza o cree una excepción para confiar en el certificado en cuestión.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="8c22f-1131">Solicitud original mediante `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="8c22f-1133">Solicitud original mediante `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="8c22f-1135">Reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1135">URL rewrite</span></span>

<span data-ttu-id="8c22f-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para crear una regla para reescribir URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="8c22f-1137">El primer parámetro contiene la expresión regular para buscar coincidencias en la ruta de dirección de URL entrante.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="8c22f-1138">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="8c22f-1139">El tercer parámetro, `skipRemainingRules: {true|false}`, indica al middleware si al aplicar la regla actual tiene que omitir o no alguna regla de redirección adicional.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="8c22f-1140">Solicitud original: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="8c22f-1142">El acento circunflejo (`^`) al principio de la expresión significa que la búsqueda de coincidencias empieza al principio de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="8c22f-1143">En el ejemplo anterior de la regla de redireccionamiento (`redirect-rule/(.*)`), no hay ningún acento circunflejo (`^`) al principio de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="8c22f-1144">Por tanto, cualquier carácter puede preceder a `redirect-rule/` en la ruta de acceso para que la coincidencia sea correcta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="8c22f-1145">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="8c22f-1145">Path</span></span>                               | <span data-ttu-id="8c22f-1146">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="8c22f-1146">Match</span></span> |
| ---
<span data-ttu-id="8c22f-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1148">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1148">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1149">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1149">'Identity'</span></span>
- <span data-ttu-id="8c22f-1150">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1151">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1151">'Razor'</span></span>
- <span data-ttu-id="8c22f-1152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1154">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1154">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1155">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1155">'Identity'</span></span>
- <span data-ttu-id="8c22f-1156">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1157">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1157">'Razor'</span></span>
- <span data-ttu-id="8c22f-1158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1160">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1160">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1161">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1161">'Identity'</span></span>
- <span data-ttu-id="8c22f-1162">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1163">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1163">'Razor'</span></span>
- <span data-ttu-id="8c22f-1164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1166">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1166">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1167">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1167">'Identity'</span></span>
- <span data-ttu-id="8c22f-1168">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1169">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1169">'Razor'</span></span>
- <span data-ttu-id="8c22f-1170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1172">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1172">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1173">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1173">'Identity'</span></span>
- <span data-ttu-id="8c22f-1174">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1175">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1175">'Razor'</span></span>
- <span data-ttu-id="8c22f-1176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1178">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1178">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1179">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1179">'Identity'</span></span>
- <span data-ttu-id="8c22f-1180">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1181">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1181">'Razor'</span></span>
- <span data-ttu-id="8c22f-1182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1184">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1184">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1185">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1185">'Identity'</span></span>
- <span data-ttu-id="8c22f-1186">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1187">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1187">'Razor'</span></span>
- <span data-ttu-id="8c22f-1188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1190">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1190">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1191">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1191">'Identity'</span></span>
- <span data-ttu-id="8c22f-1192">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1193">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1193">'Razor'</span></span>
- <span data-ttu-id="8c22f-1194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1196">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1196">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1197">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1197">'Identity'</span></span>
- <span data-ttu-id="8c22f-1198">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1199">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1199">'Razor'</span></span>
- <span data-ttu-id="8c22f-1200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1202">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1202">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1203">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1203">'Identity'</span></span>
- <span data-ttu-id="8c22f-1204">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1205">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1205">'Razor'</span></span>
- <span data-ttu-id="8c22f-1206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1208">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1208">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1209">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1209">'Identity'</span></span>
- <span data-ttu-id="8c22f-1210">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1211">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1211">'Razor'</span></span>
- <span data-ttu-id="8c22f-1212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1214">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1214">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1215">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1215">'Identity'</span></span>
- <span data-ttu-id="8c22f-1216">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1217">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1217">'Razor'</span></span>
- <span data-ttu-id="8c22f-1218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1220">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1220">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1221">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1221">'Identity'</span></span>
- <span data-ttu-id="8c22f-1222">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1223">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1223">'Razor'</span></span>
- <span data-ttu-id="8c22f-1224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1226">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1226">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1227">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1227">'Identity'</span></span>
- <span data-ttu-id="8c22f-1228">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1229">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1229">'Razor'</span></span>
- <span data-ttu-id="8c22f-1230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1232">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1232">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1233">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1233">'Identity'</span></span>
- <span data-ttu-id="8c22f-1234">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1235">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1235">'Razor'</span></span>
- <span data-ttu-id="8c22f-1236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sí   | | `/my-cool-redirect-rule/1234/5678` | Sí   | | `/anotherredirect-rule/1234/5678`  | Sí   |</span><span class="sxs-lookup"><span data-stu-id="8c22f-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="8c22f-1238">La regla de reescritura, `^rewrite-rule/(\d+)/(\d+)`, solo encuentra rutas de acceso que empiezan con `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="8c22f-1239">En la tabla siguiente, observe la diferencia de coincidencia.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="8c22f-1240">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="8c22f-1240">Path</span></span>                              | <span data-ttu-id="8c22f-1241">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="8c22f-1241">Match</span></span> |
| ---
<span data-ttu-id="8c22f-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1243">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1243">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1244">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1244">'Identity'</span></span>
- <span data-ttu-id="8c22f-1245">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1246">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1246">'Razor'</span></span>
- <span data-ttu-id="8c22f-1247">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1249">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1249">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1250">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1250">'Identity'</span></span>
- <span data-ttu-id="8c22f-1251">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1252">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1252">'Razor'</span></span>
- <span data-ttu-id="8c22f-1253">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1255">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1255">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1256">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1256">'Identity'</span></span>
- <span data-ttu-id="8c22f-1257">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1258">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1258">'Razor'</span></span>
- <span data-ttu-id="8c22f-1259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1261">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1261">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1262">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1262">'Identity'</span></span>
- <span data-ttu-id="8c22f-1263">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1264">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1264">'Razor'</span></span>
- <span data-ttu-id="8c22f-1265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1267">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1267">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1268">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1268">'Identity'</span></span>
- <span data-ttu-id="8c22f-1269">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1270">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1270">'Razor'</span></span>
- <span data-ttu-id="8c22f-1271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1273">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1273">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1274">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1274">'Identity'</span></span>
- <span data-ttu-id="8c22f-1275">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1276">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1276">'Razor'</span></span>
- <span data-ttu-id="8c22f-1277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1279">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1279">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1280">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1280">'Identity'</span></span>
- <span data-ttu-id="8c22f-1281">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1282">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1282">'Razor'</span></span>
- <span data-ttu-id="8c22f-1283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1285">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1285">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1286">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1286">'Identity'</span></span>
- <span data-ttu-id="8c22f-1287">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1288">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1288">'Razor'</span></span>
- <span data-ttu-id="8c22f-1289">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1291">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1291">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1292">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1292">'Identity'</span></span>
- <span data-ttu-id="8c22f-1293">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1294">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1294">'Razor'</span></span>
- <span data-ttu-id="8c22f-1295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1297">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1297">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1298">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1298">'Identity'</span></span>
- <span data-ttu-id="8c22f-1299">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1300">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1300">'Razor'</span></span>
- <span data-ttu-id="8c22f-1301">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1303">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1303">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1304">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1304">'Identity'</span></span>
- <span data-ttu-id="8c22f-1305">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1306">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1306">'Razor'</span></span>
- <span data-ttu-id="8c22f-1307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1309">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1309">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1310">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1310">'Identity'</span></span>
- <span data-ttu-id="8c22f-1311">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1312">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1312">'Razor'</span></span>
- <span data-ttu-id="8c22f-1313">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1315">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1315">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1316">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1316">'Identity'</span></span>
- <span data-ttu-id="8c22f-1317">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1318">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1318">'Razor'</span></span>
- <span data-ttu-id="8c22f-1319">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1321">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1321">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1322">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1322">'Identity'</span></span>
- <span data-ttu-id="8c22f-1323">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1324">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1324">'Razor'</span></span>
- <span data-ttu-id="8c22f-1325">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sí   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span><span class="sxs-lookup"><span data-stu-id="8c22f-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="8c22f-1327">Después de la parte `^rewrite-rule/` de la expresión, hay dos grupos de captura, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="8c22f-1328">`\d` significa *buscar un dígito (número)* .</span><span class="sxs-lookup"><span data-stu-id="8c22f-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="8c22f-1329">El signo más (`+`) significa *buscar uno o más de los caracteres anteriores*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="8c22f-1330">Por tanto, la URL debe contener un número seguido de una barra diagonal, seguida de otro número.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="8c22f-1331">Estos grupos de capturas se insertan en la URL de reescritura como `$1` y `$2`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="8c22f-1332">La cadena de reemplazo de la regla de reescritura coloca los grupos capturados en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="8c22f-1333">La ruta de acceso solicitada de `/rewrite-rule/1234/5678` se reescribe para obtener el recurso en `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="8c22f-1334">Si una cadena de consulta está presente en la solicitud original, se conserva cuando se reescribe la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="8c22f-1335">No hay ningún recorrido de ida y vuelta al servidor para obtener el recurso.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="8c22f-1336">Si el recurso existe, se captura y se devuelve al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="8c22f-1337">Como el cliente no se redirige, la dirección URL no cambia en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="8c22f-1338">Los clientes no pueden detectar que se ha producido una operación de reescritura de URL en el servidor.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="8c22f-1339">Use `skipRemainingRules: true` siempre que sea posible, ya que las reglas de coincidencia consumen muchos recursos y aumentan el tiempo de respuesta de aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="8c22f-1340">Para obtener la respuesta más rápida de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="8c22f-1341">Ordene las reglas de reescritura desde la que coincida con más frecuencia a la que coincida con menos frecuencia.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="8c22f-1342">Omita el procesamiento de las reglas restantes cuando se produzca una coincidencia; no es necesario ningún procesamiento de reglas adicional.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="8c22f-1343">mod_rewrite de Apache</span><span class="sxs-lookup"><span data-stu-id="8c22f-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="8c22f-1344">Aplique reglas mod_rewrite de Apache con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="8c22f-1345">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8c22f-1346">Para obtener más información y ejemplos de reglas mod_rewrite, vea [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="8c22f-1347">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="8c22f-1348">La aplicación de ejemplo redirige las solicitudes de `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="8c22f-1349">El código de estado de la respuesta es *302: Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="8c22f-1350">Solicitud original: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="8c22f-1352">El middleware admite las siguientes variables de servidor mod_rewrite de Apache:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="8c22f-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="8c22f-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8c22f-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8c22f-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8c22f-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8c22f-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="8c22f-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="8c22f-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="8c22f-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8c22f-1358">HTTP_HOST</span></span>
* <span data-ttu-id="8c22f-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8c22f-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="8c22f-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8c22f-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8c22f-1361">HTTPS</span></span>
* <span data-ttu-id="8c22f-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="8c22f-1362">IPV6</span></span>
* <span data-ttu-id="8c22f-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8c22f-1363">QUERY_STRING</span></span>
* <span data-ttu-id="8c22f-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="8c22f-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="8c22f-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8c22f-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8c22f-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="8c22f-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="8c22f-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="8c22f-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="8c22f-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8c22f-1369">REQUEST_URI</span></span>
* <span data-ttu-id="8c22f-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8c22f-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="8c22f-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="8c22f-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1372">SERVER_PORT</span></span>
* <span data-ttu-id="8c22f-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="8c22f-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="8c22f-1374">TIME</span></span>
* <span data-ttu-id="8c22f-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="8c22f-1375">TIME_DAY</span></span>
* <span data-ttu-id="8c22f-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1376">TIME_HOUR</span></span>
* <span data-ttu-id="8c22f-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="8c22f-1377">TIME_MIN</span></span>
* <span data-ttu-id="8c22f-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="8c22f-1378">TIME_MON</span></span>
* <span data-ttu-id="8c22f-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="8c22f-1379">TIME_SEC</span></span>
* <span data-ttu-id="8c22f-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="8c22f-1380">TIME_WDAY</span></span>
* <span data-ttu-id="8c22f-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="8c22f-1382">Reglas del Módulo URL Rewrite para IIS</span><span class="sxs-lookup"><span data-stu-id="8c22f-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="8c22f-1383">Para usar el mismo conjunto de reglas que se aplica al módulo URL Rewrite para IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="8c22f-1384">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8c22f-1385">No dirija el middleware para que use el archivo *web.config* de la aplicación cuando se ejecute en Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="8c22f-1386">Con IIS, estas reglas se deben almacenar fuera del archivo *web.config* de la aplicación para evitar conflictos con el Módulo URL Rewrite para IIS.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="8c22f-1387">Para obtener más información y ejemplos de reglas del Módulo URL Rewrite para IIS, vea [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0) y [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="8c22f-1388">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="8c22f-1389">La aplicación de ejemplo reescribe las solicitudes de `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="8c22f-1390">La respuesta se envía al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="8c22f-1391">Solicitud original: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="8c22f-1393">Si tiene un Módulo URL Rewrite para IIS activo con reglas configuradas en el nivel de servidor que podrían afectar a la aplicación de manera no deseada, puede deshabilitar el Módulo URL Rewrite para IIS para una aplicación.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="8c22f-1394">Para más información, vea [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Deshabilitación de módulos de IIS).</span><span class="sxs-lookup"><span data-stu-id="8c22f-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="8c22f-1395">Características no admitidas</span><span class="sxs-lookup"><span data-stu-id="8c22f-1395">Unsupported features</span></span>

<span data-ttu-id="8c22f-1396">El middleware publicado con ASP.NET Core 2.x no admite las siguientes características de Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="8c22f-1397">Reglas de salida</span><span class="sxs-lookup"><span data-stu-id="8c22f-1397">Outbound Rules</span></span>
* <span data-ttu-id="8c22f-1398">Variables de servidor personalizadas</span><span class="sxs-lookup"><span data-stu-id="8c22f-1398">Custom Server Variables</span></span>
* <span data-ttu-id="8c22f-1399">Caracteres comodín</span><span class="sxs-lookup"><span data-stu-id="8c22f-1399">Wildcards</span></span>
* <span data-ttu-id="8c22f-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="8c22f-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="8c22f-1401">Variables de servidor compatibles</span><span class="sxs-lookup"><span data-stu-id="8c22f-1401">Supported server variables</span></span>

<span data-ttu-id="8c22f-1402">El middleware admite las siguientes variables de servidor del Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="8c22f-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="8c22f-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="8c22f-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="8c22f-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="8c22f-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8c22f-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8c22f-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8c22f-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8c22f-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="8c22f-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8c22f-1408">HTTP_HOST</span></span>
* <span data-ttu-id="8c22f-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8c22f-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="8c22f-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1410">HTTP_URL</span></span>
* <span data-ttu-id="8c22f-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8c22f-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8c22f-1412">HTTPS</span></span>
* <span data-ttu-id="8c22f-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="8c22f-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8c22f-1414">QUERY_STRING</span></span>
* <span data-ttu-id="8c22f-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8c22f-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="8c22f-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8c22f-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="8c22f-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8c22f-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8c22f-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8c22f-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="8c22f-1419">También puede obtener <xref:Microsoft.Extensions.FileProviders.IFileProvider> a través de <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="8c22f-1420">Con este enfoque logrará mayor flexibilidad para la ubicación de los archivos de reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="8c22f-1421">Asegúrese de que los archivos de reglas de reescritura se implementan en el servidor en la ruta de acceso que proporcione.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="8c22f-1422">Regla basada en métodos</span><span class="sxs-lookup"><span data-stu-id="8c22f-1422">Method-based rule</span></span>

<span data-ttu-id="8c22f-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar su propia lógica de la regla en un método.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="8c22f-1424">`Add` expone el elemento <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, lo que hace que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible para usarlo en el método.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="8c22f-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina cómo se administra el procesamiento adicional en la canalización.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="8c22f-1426">Establezca el valor en uno de los campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> que se describen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="8c22f-1427">Acción</span><span class="sxs-lookup"><span data-stu-id="8c22f-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="8c22f-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1429">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1429">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1430">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1430">'Identity'</span></span>
- <span data-ttu-id="8c22f-1431">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1432">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1432">'Razor'</span></span>
- <span data-ttu-id="8c22f-1433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1435">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1435">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1436">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1436">'Identity'</span></span>
- <span data-ttu-id="8c22f-1437">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1438">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1438">'Razor'</span></span>
- <span data-ttu-id="8c22f-1439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1441">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1441">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1442">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1442">'Identity'</span></span>
- <span data-ttu-id="8c22f-1443">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1444">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1444">'Razor'</span></span>
- <span data-ttu-id="8c22f-1445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1447">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1447">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1448">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1448">'Identity'</span></span>
- <span data-ttu-id="8c22f-1449">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1450">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1450">'Razor'</span></span>
- <span data-ttu-id="8c22f-1451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1453">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1453">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1454">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1454">'Identity'</span></span>
- <span data-ttu-id="8c22f-1455">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1456">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1456">'Razor'</span></span>
- <span data-ttu-id="8c22f-1457">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1459">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1459">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1460">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1460">'Identity'</span></span>
- <span data-ttu-id="8c22f-1461">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1462">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1462">'Razor'</span></span>
- <span data-ttu-id="8c22f-1463">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1465">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1465">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1466">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1466">'Identity'</span></span>
- <span data-ttu-id="8c22f-1467">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1468">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1468">'Razor'</span></span>
- <span data-ttu-id="8c22f-1469">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1471">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1471">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1472">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1472">'Identity'</span></span>
- <span data-ttu-id="8c22f-1473">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1474">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1474">'Razor'</span></span>
- <span data-ttu-id="8c22f-1475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1477">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1477">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1478">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1478">'Identity'</span></span>
- <span data-ttu-id="8c22f-1479">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1480">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1480">'Razor'</span></span>
- <span data-ttu-id="8c22f-1481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1483">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1483">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1484">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1484">'Identity'</span></span>
- <span data-ttu-id="8c22f-1485">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1486">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1486">'Razor'</span></span>
- <span data-ttu-id="8c22f-1487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1489">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1489">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1490">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1490">'Identity'</span></span>
- <span data-ttu-id="8c22f-1491">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1492">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1492">'Razor'</span></span>
- <span data-ttu-id="8c22f-1493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1495">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1495">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1496">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1496">'Identity'</span></span>
- <span data-ttu-id="8c22f-1497">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1498">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1498">'Razor'</span></span>
- <span data-ttu-id="8c22f-1499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1501">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1501">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1502">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1502">'Identity'</span></span>
- <span data-ttu-id="8c22f-1503">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1504">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1504">'Razor'</span></span>
- <span data-ttu-id="8c22f-1505">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1507">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1507">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1508">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1508">'Identity'</span></span>
- <span data-ttu-id="8c22f-1509">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1510">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1510">'Razor'</span></span>
- <span data-ttu-id="8c22f-1511">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1513">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1513">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1514">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1514">'Identity'</span></span>
- <span data-ttu-id="8c22f-1515">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1516">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1516">'Razor'</span></span>
- <span data-ttu-id="8c22f-1517">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1519">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1519">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1520">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1520">'Identity'</span></span>
- <span data-ttu-id="8c22f-1521">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1522">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1522">'Razor'</span></span>
- <span data-ttu-id="8c22f-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1525">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1525">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1526">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1526">'Identity'</span></span>
- <span data-ttu-id="8c22f-1527">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1528">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1528">'Razor'</span></span>
- <span data-ttu-id="8c22f-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1531">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1531">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1532">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1532">'Identity'</span></span>
- <span data-ttu-id="8c22f-1533">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1534">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1534">'Razor'</span></span>
- <span data-ttu-id="8c22f-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1537">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1537">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1538">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1538">'Identity'</span></span>
- <span data-ttu-id="8c22f-1539">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1540">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1540">'Razor'</span></span>
- <span data-ttu-id="8c22f-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1543">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1543">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1544">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1544">'Identity'</span></span>
- <span data-ttu-id="8c22f-1545">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1546">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1546">'Razor'</span></span>
- <span data-ttu-id="8c22f-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1549">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1549">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1550">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1550">'Identity'</span></span>
- <span data-ttu-id="8c22f-1551">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1552">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1552">'Razor'</span></span>
- <span data-ttu-id="8c22f-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1555">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1555">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1556">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1556">'Identity'</span></span>
- <span data-ttu-id="8c22f-1557">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1558">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1558">'Razor'</span></span>
- <span data-ttu-id="8c22f-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1561">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1561">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1562">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1562">'Identity'</span></span>
- <span data-ttu-id="8c22f-1563">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1564">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1564">'Razor'</span></span>
- <span data-ttu-id="8c22f-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1567">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1567">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1568">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1568">'Identity'</span></span>
- <span data-ttu-id="8c22f-1569">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1570">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1570">'Razor'</span></span>
- <span data-ttu-id="8c22f-1571">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1573">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1573">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1574">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1574">'Identity'</span></span>
- <span data-ttu-id="8c22f-1575">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1576">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1576">'Razor'</span></span>
- <span data-ttu-id="8c22f-1577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1579">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1579">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1580">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1580">'Identity'</span></span>
- <span data-ttu-id="8c22f-1581">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1582">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1582">'Razor'</span></span>
- <span data-ttu-id="8c22f-1583">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1585">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1585">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1586">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1586">'Identity'</span></span>
- <span data-ttu-id="8c22f-1587">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1588">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1588">'Razor'</span></span>
- <span data-ttu-id="8c22f-1589">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1591">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1591">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1592">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1592">'Identity'</span></span>
- <span data-ttu-id="8c22f-1593">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1594">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1594">'Razor'</span></span>
- <span data-ttu-id="8c22f-1595">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1597">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1597">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1598">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1598">'Identity'</span></span>
- <span data-ttu-id="8c22f-1599">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1600">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1600">'Razor'</span></span>
- <span data-ttu-id="8c22f-1601">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1603">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1603">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1604">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1604">'Identity'</span></span>
- <span data-ttu-id="8c22f-1605">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1606">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1606">'Razor'</span></span>
- <span data-ttu-id="8c22f-1607">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1609">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1609">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1610">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1610">'Identity'</span></span>
- <span data-ttu-id="8c22f-1611">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1612">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1612">'Razor'</span></span>
- <span data-ttu-id="8c22f-1613">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1615">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1615">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1616">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1616">'Identity'</span></span>
- <span data-ttu-id="8c22f-1617">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1618">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1618">'Razor'</span></span>
- <span data-ttu-id="8c22f-1619">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1621">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1621">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1622">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1622">'Identity'</span></span>
- <span data-ttu-id="8c22f-1623">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1624">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1624">'Razor'</span></span>
- <span data-ttu-id="8c22f-1625">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1627">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1627">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1628">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1628">'Identity'</span></span>
- <span data-ttu-id="8c22f-1629">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1630">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1630">'Razor'</span></span>
- <span data-ttu-id="8c22f-1631">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1633">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1633">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1634">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1634">'Identity'</span></span>
- <span data-ttu-id="8c22f-1635">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1636">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1636">'Razor'</span></span>
- <span data-ttu-id="8c22f-1637">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1639">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1639">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1640">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1640">'Identity'</span></span>
- <span data-ttu-id="8c22f-1641">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1642">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1642">'Razor'</span></span>
- <span data-ttu-id="8c22f-1643">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1645">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1645">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1646">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1646">'Identity'</span></span>
- <span data-ttu-id="8c22f-1647">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1648">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1648">'Razor'</span></span>
- <span data-ttu-id="8c22f-1649">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1651">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1651">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1652">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1652">'Identity'</span></span>
- <span data-ttu-id="8c22f-1653">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1654">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1654">'Razor'</span></span>
- <span data-ttu-id="8c22f-1655">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1657">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1657">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1658">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1658">'Identity'</span></span>
- <span data-ttu-id="8c22f-1659">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1660">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1660">'Razor'</span></span>
- <span data-ttu-id="8c22f-1661">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1663">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1663">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1664">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1664">'Identity'</span></span>
- <span data-ttu-id="8c22f-1665">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1666">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1666">'Razor'</span></span>
- <span data-ttu-id="8c22f-1667">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1669">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1669">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1670">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1670">'Identity'</span></span>
- <span data-ttu-id="8c22f-1671">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1672">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1672">'Razor'</span></span>
- <span data-ttu-id="8c22f-1673">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1675">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1675">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1676">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1676">'Identity'</span></span>
- <span data-ttu-id="8c22f-1677">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1678">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1678">'Razor'</span></span>
- <span data-ttu-id="8c22f-1679">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1681">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1681">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1682">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1682">'Identity'</span></span>
- <span data-ttu-id="8c22f-1683">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1684">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1684">'Razor'</span></span>
- <span data-ttu-id="8c22f-1685">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1687">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1687">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1688">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1688">'Identity'</span></span>
- <span data-ttu-id="8c22f-1689">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1690">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1690">'Razor'</span></span>
- <span data-ttu-id="8c22f-1691">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1693">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1693">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1694">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1694">'Identity'</span></span>
- <span data-ttu-id="8c22f-1695">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1696">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1696">'Razor'</span></span>
- <span data-ttu-id="8c22f-1697">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1699">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1699">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1700">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1700">'Identity'</span></span>
- <span data-ttu-id="8c22f-1701">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1702">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1702">'Razor'</span></span>
- <span data-ttu-id="8c22f-1703">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1704">-------------------------------- | | `RuleResult.ContinueRules` (valor predeterminado) | Continuar aplicando las reglas.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="8c22f-1705">| | `RuleResult.EndResponse`             | Dejar de aplicar las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="8c22f-1706">| | `RuleResult.SkipRemainingRules`      | Dejar de aplicar las reglas y enviar el contexto al middleware siguiente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="8c22f-1707">La aplicación de ejemplo muestra un método que redirige las solicitudes para las rutas de acceso que terminen con *.xml*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="8c22f-1708">Si se realiza una solicitud de `/file.xml`, la solicitud se redirige a `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="8c22f-1709">El código de estado se establece en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="8c22f-1710">Cuando el explorador realiza una solicitud nueva a */xmlfiles/file.xml*, el middleware de archivos estáticos sirve el archivo al cliente desde la carpeta *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="8c22f-1711">Para un redireccionamiento, debe establecer de forma explícita el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="8c22f-1712">En caso contrario, se devuelve un código de estado *200: correcto* y no se produce el redireccionamiento en el cliente.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="8c22f-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="8c22f-1714">Este enfoque también permite volver a escribir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="8c22f-1715">En la aplicación de ejemplo se muestra cómo volver a escribir la ruta de acceso para cualquier solicitud de archivo de texto para proporcionar el archivo de texto *file.txt* desde la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="8c22f-1716">El middleware de archivos estáticos proporciona el archivo en función de la ruta de acceso de la solicitud actualizada:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="8c22f-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="8c22f-1718">Regla basada en IRule</span><span class="sxs-lookup"><span data-stu-id="8c22f-1718">IRule-based rule</span></span>

<span data-ttu-id="8c22f-1719">Utilice <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar la lógica de reglas en una clase que implemente la interfaz <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="8c22f-1720">`IRule` proporciona mayor flexibilidad que si se usa el enfoque de reglas basadas en métodos.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="8c22f-1721">La clase de implementación puede incluir un constructor que permita pasar parámetros para el método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="8c22f-1722">Se comprueba que los valores de los parámetros en la aplicación de ejemplo para `extension` y `newPath` cumplen ciertas condiciones.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="8c22f-1723">`extension` debe contener un valor, que debe ser *.png*, *.jpg* o *.gif*.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="8c22f-1724">Si `newPath` no es válido, se genera <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="8c22f-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="8c22f-1725">Si se realiza una solicitud de *image.png*, la solicitud se redirige a `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="8c22f-1726">Si se realiza una solicitud de *image.jpg*, la solicitud se redirige a `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="8c22f-1727">El código de estado se establece en *301 - Movido definitivamente* y se establece `context.Result` para detener el procesamiento de las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="8c22f-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="8c22f-1728">Solicitud original: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1728">Original Request: `/image.png`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="8c22f-1730">Solicitud original: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1730">Original Request: `/image.jpg`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="8c22f-1732">Ejemplos de expresiones regulares</span><span class="sxs-lookup"><span data-stu-id="8c22f-1732">Regex examples</span></span>

| <span data-ttu-id="8c22f-1733">Objetivo</span><span class="sxs-lookup"><span data-stu-id="8c22f-1733">Goal</span></span> | <span data-ttu-id="8c22f-1734">Cadena de expresión regular &</span><span class="sxs-lookup"><span data-stu-id="8c22f-1734">Regex String &</span></span><br><span data-ttu-id="8c22f-1735">Ejemplo de coincidencia</span><span class="sxs-lookup"><span data-stu-id="8c22f-1735">Match Example</span></span> | <span data-ttu-id="8c22f-1736">Cadena de reemplazo &</span><span class="sxs-lookup"><span data-stu-id="8c22f-1736">Replacement String &</span></span><br><span data-ttu-id="8c22f-1737">Ejemplo de resultado</span><span class="sxs-lookup"><span data-stu-id="8c22f-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="8c22f-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1739">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1739">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1740">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1740">'Identity'</span></span>
- <span data-ttu-id="8c22f-1741">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1742">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1742">'Razor'</span></span>
- <span data-ttu-id="8c22f-1743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1745">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1745">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1746">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1746">'Identity'</span></span>
- <span data-ttu-id="8c22f-1747">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1748">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1748">'Razor'</span></span>
- <span data-ttu-id="8c22f-1749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1751">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1751">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1752">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1752">'Identity'</span></span>
- <span data-ttu-id="8c22f-1753">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1754">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1754">'Razor'</span></span>
- <span data-ttu-id="8c22f-1755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1757">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1757">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1758">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1758">'Identity'</span></span>
- <span data-ttu-id="8c22f-1759">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1760">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1760">'Razor'</span></span>
- <span data-ttu-id="8c22f-1761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1763">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1763">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1764">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1764">'Identity'</span></span>
- <span data-ttu-id="8c22f-1765">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1766">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1766">'Razor'</span></span>
- <span data-ttu-id="8c22f-1767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1769">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1769">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1770">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1770">'Identity'</span></span>
- <span data-ttu-id="8c22f-1771">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1772">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1772">'Razor'</span></span>
- <span data-ttu-id="8c22f-1773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1775">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1775">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1776">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1776">'Identity'</span></span>
- <span data-ttu-id="8c22f-1777">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1778">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1778">'Razor'</span></span>
- <span data-ttu-id="8c22f-1779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1781">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1781">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1782">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1782">'Identity'</span></span>
- <span data-ttu-id="8c22f-1783">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1784">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1784">'Razor'</span></span>
- <span data-ttu-id="8c22f-1785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1787">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1787">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1788">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1788">'Identity'</span></span>
- <span data-ttu-id="8c22f-1789">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1790">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1790">'Razor'</span></span>
- <span data-ttu-id="8c22f-1791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1793">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1793">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1794">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1794">'Identity'</span></span>
- <span data-ttu-id="8c22f-1795">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1796">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1796">'Razor'</span></span>
- <span data-ttu-id="8c22f-1797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1799">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1799">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1800">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1800">'Identity'</span></span>
- <span data-ttu-id="8c22f-1801">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1802">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1802">'Razor'</span></span>
- <span data-ttu-id="8c22f-1803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1805">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1805">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1806">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1806">'Identity'</span></span>
- <span data-ttu-id="8c22f-1807">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1808">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1808">'Razor'</span></span>
- <span data-ttu-id="8c22f-1809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1811">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1811">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1812">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1812">'Identity'</span></span>
- <span data-ttu-id="8c22f-1813">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1814">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1814">'Razor'</span></span>
- <span data-ttu-id="8c22f-1815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1817">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1817">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1818">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1818">'Identity'</span></span>
- <span data-ttu-id="8c22f-1819">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1820">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1820">'Razor'</span></span>
- <span data-ttu-id="8c22f-1821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1823">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1823">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1824">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1824">'Identity'</span></span>
- <span data-ttu-id="8c22f-1825">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1826">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1826">'Razor'</span></span>
- <span data-ttu-id="8c22f-1827">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1829">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1829">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1830">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1830">'Identity'</span></span>
- <span data-ttu-id="8c22f-1831">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1832">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1832">'Razor'</span></span>
- <span data-ttu-id="8c22f-1833">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1835">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1835">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1836">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1836">'Identity'</span></span>
- <span data-ttu-id="8c22f-1837">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1838">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1838">'Razor'</span></span>
- <span data-ttu-id="8c22f-1839">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1841">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1841">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1842">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1842">'Identity'</span></span>
- <span data-ttu-id="8c22f-1843">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1844">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1844">'Razor'</span></span>
- <span data-ttu-id="8c22f-1845">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1847">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1847">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1848">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1848">'Identity'</span></span>
- <span data-ttu-id="8c22f-1849">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1850">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1850">'Razor'</span></span>
- <span data-ttu-id="8c22f-1851">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1853">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1853">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1854">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1854">'Identity'</span></span>
- <span data-ttu-id="8c22f-1855">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1856">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1856">'Razor'</span></span>
- <span data-ttu-id="8c22f-1857">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1859">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1859">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1860">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1860">'Identity'</span></span>
- <span data-ttu-id="8c22f-1861">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1862">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1862">'Razor'</span></span>
- <span data-ttu-id="8c22f-1863">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1865">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1865">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1866">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1866">'Identity'</span></span>
- <span data-ttu-id="8c22f-1867">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1868">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1868">'Razor'</span></span>
- <span data-ttu-id="8c22f-1869">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1871">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1871">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1872">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1872">'Identity'</span></span>
- <span data-ttu-id="8c22f-1873">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1874">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1874">'Razor'</span></span>
- <span data-ttu-id="8c22f-1875">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1877">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1877">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1878">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1878">'Identity'</span></span>
- <span data-ttu-id="8c22f-1879">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1880">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1880">'Razor'</span></span>
- <span data-ttu-id="8c22f-1881">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1883">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1883">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1884">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1884">'Identity'</span></span>
- <span data-ttu-id="8c22f-1885">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1886">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1886">'Razor'</span></span>
- <span data-ttu-id="8c22f-1887">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1889">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1889">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1890">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1890">'Identity'</span></span>
- <span data-ttu-id="8c22f-1891">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1892">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1892">'Razor'</span></span>
- <span data-ttu-id="8c22f-1893">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1895">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1895">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1896">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1896">'Identity'</span></span>
- <span data-ttu-id="8c22f-1897">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1898">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1898">'Razor'</span></span>
- <span data-ttu-id="8c22f-1899">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1901">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1901">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1902">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1902">'Identity'</span></span>
- <span data-ttu-id="8c22f-1903">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1904">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1904">'Razor'</span></span>
- <span data-ttu-id="8c22f-1905">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1907">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1907">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1908">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1908">'Identity'</span></span>
- <span data-ttu-id="8c22f-1909">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1910">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1910">'Razor'</span></span>
- <span data-ttu-id="8c22f-1911">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8c22f-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8c22f-1913">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1913">'Blazor'</span></span>
- <span data-ttu-id="8c22f-1914">"Identity"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1914">'Identity'</span></span>
- <span data-ttu-id="8c22f-1915">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="8c22f-1916">"Razor"</span><span class="sxs-lookup"><span data-stu-id="8c22f-1916">'Razor'</span></span>
- <span data-ttu-id="8c22f-1917">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8c22f-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="8c22f-1918">------------------- | | Ruta de acceso de reescritura en la cadena de consulta | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="8c22f-1919">`/path?var1=abc&var2=123` | | Quitar barra diagonal final | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="8c22f-1920">`/path` | | Exigir barra diagonal final | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="8c22f-1921">`/path/` | | Evitar reescritura de solicitudes específicas | `^(.*)(?<!\.axd)$` o `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="8c22f-1922">Sí: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="8c22f-1923">No: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="8c22f-1924">`/resource.axd` | | Reorganizar los segmentos de URL | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="8c22f-1925">`path/3/2/1` | | Reemplazar un segmento de URL | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="8c22f-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8c22f-1926">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="8c22f-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="8c22f-1927">Expresiones regulares en .NET</span><span class="sxs-lookup"><span data-stu-id="8c22f-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="8c22f-1928">Lenguaje de expresiones regulares: referencia rápida</span><span class="sxs-lookup"><span data-stu-id="8c22f-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* <span data-ttu-id="8c22f-1929">[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache)</span><span class="sxs-lookup"><span data-stu-id="8c22f-1929">[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* <span data-ttu-id="8c22f-1930">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0 para IIS)</span><span class="sxs-lookup"><span data-stu-id="8c22f-1930">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)</span></span>
* <span data-ttu-id="8c22f-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite)</span><span class="sxs-lookup"><span data-stu-id="8c22f-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* <span data-ttu-id="8c22f-1932">[IIS URL Rewrite Module Forum](https://forums.iis.net/1152.aspx) (Foro del Módulo URL Rewrite para IIS)</span><span class="sxs-lookup"><span data-stu-id="8c22f-1932">[IIS URL Rewrite Module Forum](https://forums.iis.net/1152.aspx)</span></span>
* [<span data-ttu-id="8c22f-1933">Cómo simplificar la estructura de direcciones URL</span><span class="sxs-lookup"><span data-stu-id="8c22f-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* <span data-ttu-id="8c22f-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/) (10 trucos y consejos para reescritura de URL)</span><span class="sxs-lookup"><span data-stu-id="8c22f-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="8c22f-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html) (Usar la barra diagonal o no)</span><span class="sxs-lookup"><span data-stu-id="8c22f-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
