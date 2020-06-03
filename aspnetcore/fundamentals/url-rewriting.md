---
<span data-ttu-id="5aa11-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-102">'Blazor'</span></span>
- <span data-ttu-id="5aa11-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-103">'Identity'</span></span>
- <span data-ttu-id="5aa11-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-105">'Razor'</span></span>
- <span data-ttu-id="5aa11-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="5aa11-107">Middleware de reescritura de URL en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5aa11-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="5aa11-108">Por [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="5aa11-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5aa11-109">En este documento se ofrece una introducción a la reescritura de URL e instrucciones sobre cómo usar el middleware de reescritura de URL en aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aa11-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="5aa11-110">La reescritura de URL consiste en modificar varias URL de solicitud basadas en una o varias reglas predefinidas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="5aa11-111">La reescritura de URL crea una abstracción entre las ubicaciones de recursos y sus direcciones para que las ubicaciones y las direcciones no estén estrechamente vinculadas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="5aa11-112">La reescritura de URL es útil en varios escenarios para:</span><span class="sxs-lookup"><span data-stu-id="5aa11-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="5aa11-113">Mover o reemplazar recursos del servidor de forma temporal o permanente a la vez que se mantienen localizadores estables para esos recursos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="5aa11-114">Dividir el procesamiento de solicitudes entre otras aplicaciones o entre áreas de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="5aa11-115">Quitar, agregar o reorganizar segmentos de dirección URL en solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="5aa11-116">Optimizar direcciones URL públicas para la optimización del motor de búsqueda (SEO).</span><span class="sxs-lookup"><span data-stu-id="5aa11-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="5aa11-117">Permitir el uso de direcciones URL públicas descriptivas para ayudar a los visitantes a predecir el contenido devuelto mediante la solicitud de un recurso.</span><span class="sxs-lookup"><span data-stu-id="5aa11-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="5aa11-118">Redirigir solicitudes no seguras a puntos de conexión seguros.</span><span class="sxs-lookup"><span data-stu-id="5aa11-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="5aa11-119">Evitar la vinculación activa, en la que un sitio externo usa un recurso estático hospedado en otro sitio mediante la vinculación del recurso a su propio contenido.</span><span class="sxs-lookup"><span data-stu-id="5aa11-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="5aa11-120">La reescritura de URL puede reducir el rendimiento de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="5aa11-121">Cuando sea factible, limite el número y la complejidad de las reglas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="5aa11-122">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5aa11-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="5aa11-123">Redireccionamiento y reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="5aa11-124">La diferencia entre los términos *redirección de URL* y *reescritura de URL* es sutil, pero tiene importantes implicaciones para proporcionar recursos a los clientes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="5aa11-125">El middleware de reescritura de URL de ASP.NET Core es capaz de satisfacer las necesidades de ambos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="5aa11-126">Una *redirección de URL* implica una operación del lado cliente, donde al cliente se le indica que acceda a un recurso en una dirección distinta a la que ha solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="5aa11-127">Esto requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="5aa11-127">This requires a round trip to the server.</span></span> <span data-ttu-id="5aa11-128">La URL de redireccionamiento que se devuelve al cliente aparece en la barra de direcciones del explorador cuando el cliente realiza una nueva solicitud para el recurso.</span><span class="sxs-lookup"><span data-stu-id="5aa11-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="5aa11-129">Si `/resource` se *redirige* a `/different-resource`, el servidor responde que el cliente debe obtener el recurso en `/different-resource` con un código de estado que indica que la redirección es temporal o permanente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Se cambió temporalmente un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="5aa11-135">Al redirigir las solicitudes a otra dirección URL, se indica si la redirección es permanente o temporal mediante la especificación del código de estado con la respuesta:</span><span class="sxs-lookup"><span data-stu-id="5aa11-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="5aa11-136">El código de estado *301 - Movido definitivamente* se usa cuando el recurso tiene una nueva dirección URL permanente y se quiere indicar al cliente que todas las solicitudes futuras para el recurso deben usar la nueva dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="5aa11-137">*El cliente puede almacenar en caché la respuesta cuando se recibe un código de estado 301.*</span><span class="sxs-lookup"><span data-stu-id="5aa11-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="5aa11-138">El código de estado *302: encontrado* se usa cuando el redireccionamiento es temporal o en general está sujeto a cambios.</span><span class="sxs-lookup"><span data-stu-id="5aa11-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="5aa11-139">El código de estado 302 indica al cliente que no almacene la dirección URL y la use en el futuro.</span><span class="sxs-lookup"><span data-stu-id="5aa11-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="5aa11-140">Para obtener más información sobre los códigos de estado, consulte [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: definiciones de los códigos de estado).</span><span class="sxs-lookup"><span data-stu-id="5aa11-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="5aa11-141">La *reescritura de URL* es una operación del lado servidor que proporciona un recurso desde una dirección de recursos distinta a la que el cliente ha solicitado.</span><span class="sxs-lookup"><span data-stu-id="5aa11-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="5aa11-142">La reescritura de una dirección URL no requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="5aa11-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="5aa11-143">La dirección URL reescrita no se devuelve al cliente y no aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="5aa11-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="5aa11-144">Si `/resource` se *reescribe* como `/different-resource`, el servidor la obtiene *internamente* y devuelve el recurso en `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="5aa11-145">Aunque es posible que el cliente pueda recuperar el recurso en la dirección URL reescrita, no se le informa de que el recurso existe en la dirección URL reescrita cuando realiza su solicitud y recibe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Se cambió un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="5aa11-150">Aplicación de ejemplo de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-150">URL rewriting sample app</span></span>

<span data-ttu-id="5aa11-151">Puede explorar las características del middleware de reescritura de URL con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="5aa11-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="5aa11-152">La aplicación emplea las reglas de redireccionamiento y reescritura, y muestra la URL redirigida o reescrita para varios escenarios.</span><span class="sxs-lookup"><span data-stu-id="5aa11-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="5aa11-153">Cuándo usar el middleware de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="5aa11-154">Use el middleware de reescritura de URL cuando no pueda usar los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="5aa11-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="5aa11-155">Módulo de reescritura de URL con IIS en Windows Server</span><span class="sxs-lookup"><span data-stu-id="5aa11-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="5aa11-156">Módulo mod_rewrite de Apache en el servidor Apache</span><span class="sxs-lookup"><span data-stu-id="5aa11-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="5aa11-157">Reescritura de URL en Nginx</span><span class="sxs-lookup"><span data-stu-id="5aa11-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="5aa11-158">Además, use el middleware cuando la aplicación se hospede en el [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener).</span><span class="sxs-lookup"><span data-stu-id="5aa11-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="5aa11-159">Las principales razones para usar la tecnologías de reescritura de URL basadas en servidor en IIS, Apache y Nginx son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="5aa11-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="5aa11-160">El middleware no es compatible con todas las características de estos módulos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="5aa11-161">Algunas de las características de los módulos de servidor no funcionan con proyectos de ASP.NET Core, como las restricciones `IsFile` y `IsDirectory` del módulo de reescritura de IIS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="5aa11-162">En estos casos, es mejor usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="5aa11-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="5aa11-163">El rendimiento del middleware probablemente no coincida con el de los módulos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="5aa11-164">La única manera de saber con certeza con qué enfoque el rendimiento disminuye más, o si la disminución de este es insignificante, es mediante una comparación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="5aa11-165">Package</span><span class="sxs-lookup"><span data-stu-id="5aa11-165">Package</span></span>

<span data-ttu-id="5aa11-166">El middleware de reescritura de URL está disponible en el paquete [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), incluido implícitamente en las aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aa11-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="5aa11-167">Extensión y opciones</span><span class="sxs-lookup"><span data-stu-id="5aa11-167">Extension and options</span></span>

<span data-ttu-id="5aa11-168">Establezca las reglas de reescritura y redirección de URL mediante la creación de una instancia de la clase [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con métodos de extensión para cada una de las reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="5aa11-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="5aa11-169">Encadene varias reglas en el orden que quiera procesarlas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="5aa11-170">Las `RewriteOptions` se pasan al middleware de reescritura de URL cuando se agrega a la canalización de solicitudes con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="5aa11-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="5aa11-171">Redirigir solicitudes que distintas de www a www</span><span class="sxs-lookup"><span data-stu-id="5aa11-171">Redirect non-www to www</span></span>

<span data-ttu-id="5aa11-172">Hay tres opciones que permiten a la aplicación redirigir solicitudes distintas de `www` a `www`:</span><span class="sxs-lookup"><span data-stu-id="5aa11-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="5aa11-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: redirige permanentemente la solicitud al subdominio `www` si la solicitud es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="5aa11-174">Se redirige con un código de estado [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="5aa11-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="5aa11-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: redirige la solicitud al subdominio `www` si la solicitud de entrada es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="5aa11-176">Se redirige con un código de estado [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="5aa11-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="5aa11-177">Una sobrecarga permite proporcionar el código de estado para la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="5aa11-178">Use un campo de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para una asignación de código de estado.</span><span class="sxs-lookup"><span data-stu-id="5aa11-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="5aa11-179">Redirección de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-179">URL redirect</span></span>

<span data-ttu-id="5aa11-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirigir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="5aa11-181">El primer parámetro contiene la expresión regular para hacer coincidir la ruta de acceso de la URL entrante.</span><span class="sxs-lookup"><span data-stu-id="5aa11-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="5aa11-182">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="5aa11-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="5aa11-183">El tercer parámetro, si está presente, especifica el código de estado.</span><span class="sxs-lookup"><span data-stu-id="5aa11-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="5aa11-184">Si no se especifica el código de estado, el valor predeterminado es *302 - Encontrado*, lo que indica que el recurso se ha movido o reemplazado temporalmente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="5aa11-185">En un explorador con herramientas de desarrollo habilitadas, realice una solicitud a la aplicación de ejemplo con la ruta de acceso `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="5aa11-186">La expresión regular coincide con la ruta de acceso de la solicitud en `redirect-rule/(.*)` y la ruta de acceso se reemplaza con `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="5aa11-187">La dirección URL de redireccionamiento se devuelve al cliente con un código de estado *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="5aa11-188">El explorador realiza una solicitud nueva en la URL de redireccionamiento, que aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="5aa11-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="5aa11-189">Como ninguna de las reglas de la aplicación de ejemplo coincide con la dirección URL de redireccionamiento:</span><span class="sxs-lookup"><span data-stu-id="5aa11-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="5aa11-190">La segunda solicitud recibe una respuesta *200: correcto* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="5aa11-191">En el cuerpo de la respuesta se muestra la dirección URL de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="5aa11-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="5aa11-192">Se realiza un recorrido de ida y vuelta al servidor cuando se *redirige* una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="5aa11-193">Tenga cuidado al establecer las reglas de redirección.</span><span class="sxs-lookup"><span data-stu-id="5aa11-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="5aa11-194">Las reglas de redirección se evalúan en cada solicitud enviada a la aplicación, incluso después de una redirección.</span><span class="sxs-lookup"><span data-stu-id="5aa11-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="5aa11-195">Es fácil crear por error un *bucle de redirecciones infinitas*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="5aa11-196">Solicitud original: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="5aa11-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="5aa11-198">La parte de la expresión incluida entre paréntesis se denomina *grupo de capturas*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="5aa11-199">El punto (`.`) de la expresión significa *buscar cualquier carácter*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="5aa11-200">El asterisco (`*`) indica *buscar el carácter precedente cero o más veces*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="5aa11-201">Por tanto, el grupo de capturas `(.*)` busca los dos últimos segmentos de la ruta de acceso de la URL, `1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="5aa11-202">Cualquier valor que se proporcione en la URL de la solicitud después de `redirect-rule/` es capturado por este grupo de capturas único.</span><span class="sxs-lookup"><span data-stu-id="5aa11-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="5aa11-203">En la cadena de reemplazo, se insertan los grupos capturados en la cadena con el signo de dólar (`$`) seguido del número de secuencia de la captura.</span><span class="sxs-lookup"><span data-stu-id="5aa11-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="5aa11-204">Se obtiene el valor del primer grupo de capturas con `$1`, el segundo con `$2`, y así siguen en secuencia para los grupos de capturas de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="5aa11-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="5aa11-205">Solo hay un grupo capturado en la expresión regular de la regla de redirección de la aplicación de ejemplo, por lo que solo hay un grupo insertado en la cadena de reemplazo, que es `$1`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="5aa11-206">Cuando se aplica la regla, la URL se convierte en `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="5aa11-207">Redirección de URL a un punto de conexión segura</span><span class="sxs-lookup"><span data-stu-id="5aa11-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="5aa11-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirigir las solicitudes HTTP al mismo host y ruta de acceso mediante el protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="5aa11-209">Si no se proporciona el código de estado, el middleware muestra de forma predeterminada *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="5aa11-210">Si no se proporciona el puerto:</span><span class="sxs-lookup"><span data-stu-id="5aa11-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="5aa11-211">El middleware tiene como valor predeterminado `null`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="5aa11-212">El esquema cambia a `https` (protocolo HTTPS), y el cliente accede al recurso en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="5aa11-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="5aa11-213">En el ejemplo siguiente se muestra cómo establecer el código de estado en *301 - Movido definitivamente* y cambiar el puerto a 5001.</span><span class="sxs-lookup"><span data-stu-id="5aa11-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="5aa11-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirigir las solicitudes no seguras al mismo host y ruta de acceso mediante el protocolo HTTPS seguro en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="5aa11-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="5aa11-215">El middleware establece el código de estado en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="5aa11-216">Al redirigir a un punto de conexión seguro sin la necesidad de reglas de redirección adicionales, se recomienda usar el Middleware de redirección de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="5aa11-217">Para más información, vea el tema [Aplicación de HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="5aa11-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="5aa11-218">La aplicación de ejemplo es capaz de mostrar cómo usar `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="5aa11-219">Agregue el método de extensión a `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="5aa11-220">Realice una solicitud poco segura a la aplicación en cualquier URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="5aa11-221">Descarte la advertencia de seguridad del explorador que indica que el certificado autofirmado no es de confianza o cree una excepción para confiar en el certificado en cuestión.</span><span class="sxs-lookup"><span data-stu-id="5aa11-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="5aa11-222">Solicitud original mediante `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="5aa11-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="5aa11-224">Solicitud original mediante `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="5aa11-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="5aa11-226">Reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-226">URL rewrite</span></span>

<span data-ttu-id="5aa11-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para crear una regla para reescribir URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="5aa11-228">El primer parámetro contiene la expresión regular para buscar coincidencias en la ruta de dirección de URL entrante.</span><span class="sxs-lookup"><span data-stu-id="5aa11-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="5aa11-229">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="5aa11-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="5aa11-230">El tercer parámetro, `skipRemainingRules: {true|false}`, indica al middleware si al aplicar la regla actual tiene que omitir o no alguna regla de redirección adicional.</span><span class="sxs-lookup"><span data-stu-id="5aa11-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="5aa11-231">Solicitud original: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="5aa11-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="5aa11-233">El acento circunflejo (`^`) al principio de la expresión significa que la búsqueda de coincidencias empieza al principio de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="5aa11-234">En el ejemplo anterior de la regla de redireccionamiento (`redirect-rule/(.*)`), no hay ningún acento circunflejo (`^`) al principio de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="5aa11-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="5aa11-235">Por tanto, cualquier carácter puede preceder a `redirect-rule/` en la ruta de acceso para que la coincidencia sea correcta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="5aa11-236">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="5aa11-236">Path</span></span>                               | <span data-ttu-id="5aa11-237">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="5aa11-237">Match</span></span> |
| ---
<span data-ttu-id="5aa11-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-239">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-239">'Blazor'</span></span>
- <span data-ttu-id="5aa11-240">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-240">'Identity'</span></span>
- <span data-ttu-id="5aa11-241">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-242">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-242">'Razor'</span></span>
- <span data-ttu-id="5aa11-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-245">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-245">'Blazor'</span></span>
- <span data-ttu-id="5aa11-246">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-246">'Identity'</span></span>
- <span data-ttu-id="5aa11-247">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-248">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-248">'Razor'</span></span>
- <span data-ttu-id="5aa11-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-251">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-251">'Blazor'</span></span>
- <span data-ttu-id="5aa11-252">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-252">'Identity'</span></span>
- <span data-ttu-id="5aa11-253">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-254">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-254">'Razor'</span></span>
- <span data-ttu-id="5aa11-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-257">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-257">'Blazor'</span></span>
- <span data-ttu-id="5aa11-258">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-258">'Identity'</span></span>
- <span data-ttu-id="5aa11-259">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-260">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-260">'Razor'</span></span>
- <span data-ttu-id="5aa11-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-263">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-263">'Blazor'</span></span>
- <span data-ttu-id="5aa11-264">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-264">'Identity'</span></span>
- <span data-ttu-id="5aa11-265">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-266">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-266">'Razor'</span></span>
- <span data-ttu-id="5aa11-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-269">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-269">'Blazor'</span></span>
- <span data-ttu-id="5aa11-270">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-270">'Identity'</span></span>
- <span data-ttu-id="5aa11-271">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-272">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-272">'Razor'</span></span>
- <span data-ttu-id="5aa11-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-275">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-275">'Blazor'</span></span>
- <span data-ttu-id="5aa11-276">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-276">'Identity'</span></span>
- <span data-ttu-id="5aa11-277">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-278">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-278">'Razor'</span></span>
- <span data-ttu-id="5aa11-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-281">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-281">'Blazor'</span></span>
- <span data-ttu-id="5aa11-282">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-282">'Identity'</span></span>
- <span data-ttu-id="5aa11-283">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-284">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-284">'Razor'</span></span>
- <span data-ttu-id="5aa11-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-287">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-287">'Blazor'</span></span>
- <span data-ttu-id="5aa11-288">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-288">'Identity'</span></span>
- <span data-ttu-id="5aa11-289">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-290">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-290">'Razor'</span></span>
- <span data-ttu-id="5aa11-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-293">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-293">'Blazor'</span></span>
- <span data-ttu-id="5aa11-294">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-294">'Identity'</span></span>
- <span data-ttu-id="5aa11-295">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-296">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-296">'Razor'</span></span>
- <span data-ttu-id="5aa11-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-299">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-299">'Blazor'</span></span>
- <span data-ttu-id="5aa11-300">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-300">'Identity'</span></span>
- <span data-ttu-id="5aa11-301">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-302">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-302">'Razor'</span></span>
- <span data-ttu-id="5aa11-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-305">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-305">'Blazor'</span></span>
- <span data-ttu-id="5aa11-306">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-306">'Identity'</span></span>
- <span data-ttu-id="5aa11-307">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-308">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-308">'Razor'</span></span>
- <span data-ttu-id="5aa11-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-311">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-311">'Blazor'</span></span>
- <span data-ttu-id="5aa11-312">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-312">'Identity'</span></span>
- <span data-ttu-id="5aa11-313">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-314">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-314">'Razor'</span></span>
- <span data-ttu-id="5aa11-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-317">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-317">'Blazor'</span></span>
- <span data-ttu-id="5aa11-318">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-318">'Identity'</span></span>
- <span data-ttu-id="5aa11-319">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-320">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-320">'Razor'</span></span>
- <span data-ttu-id="5aa11-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-323">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-323">'Blazor'</span></span>
- <span data-ttu-id="5aa11-324">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-324">'Identity'</span></span>
- <span data-ttu-id="5aa11-325">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-326">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-326">'Razor'</span></span>
- <span data-ttu-id="5aa11-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-327">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sí   | | `/my-cool-redirect-rule/1234/5678` | Sí   | | `/anotherredirect-rule/1234/5678`  | Sí   |</span><span class="sxs-lookup"><span data-stu-id="5aa11-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="5aa11-329">La regla de reescritura, `^rewrite-rule/(\d+)/(\d+)`, solo encuentra rutas de acceso que empiezan con `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="5aa11-330">En la tabla siguiente, observe la diferencia de coincidencia.</span><span class="sxs-lookup"><span data-stu-id="5aa11-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="5aa11-331">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="5aa11-331">Path</span></span>                              | <span data-ttu-id="5aa11-332">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="5aa11-332">Match</span></span> |
| ---
<span data-ttu-id="5aa11-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-334">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-334">'Blazor'</span></span>
- <span data-ttu-id="5aa11-335">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-335">'Identity'</span></span>
- <span data-ttu-id="5aa11-336">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-337">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-337">'Razor'</span></span>
- <span data-ttu-id="5aa11-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-340">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-340">'Blazor'</span></span>
- <span data-ttu-id="5aa11-341">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-341">'Identity'</span></span>
- <span data-ttu-id="5aa11-342">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-343">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-343">'Razor'</span></span>
- <span data-ttu-id="5aa11-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-346">'Blazor'</span></span>
- <span data-ttu-id="5aa11-347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-347">'Identity'</span></span>
- <span data-ttu-id="5aa11-348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-349">'Razor'</span></span>
- <span data-ttu-id="5aa11-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-352">'Blazor'</span></span>
- <span data-ttu-id="5aa11-353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-353">'Identity'</span></span>
- <span data-ttu-id="5aa11-354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-355">'Razor'</span></span>
- <span data-ttu-id="5aa11-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-358">'Blazor'</span></span>
- <span data-ttu-id="5aa11-359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-359">'Identity'</span></span>
- <span data-ttu-id="5aa11-360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-361">'Razor'</span></span>
- <span data-ttu-id="5aa11-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-364">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-364">'Blazor'</span></span>
- <span data-ttu-id="5aa11-365">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-365">'Identity'</span></span>
- <span data-ttu-id="5aa11-366">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-367">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-367">'Razor'</span></span>
- <span data-ttu-id="5aa11-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-370">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-370">'Blazor'</span></span>
- <span data-ttu-id="5aa11-371">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-371">'Identity'</span></span>
- <span data-ttu-id="5aa11-372">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-373">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-373">'Razor'</span></span>
- <span data-ttu-id="5aa11-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-376">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-376">'Blazor'</span></span>
- <span data-ttu-id="5aa11-377">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-377">'Identity'</span></span>
- <span data-ttu-id="5aa11-378">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-379">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-379">'Razor'</span></span>
- <span data-ttu-id="5aa11-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-382">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-382">'Blazor'</span></span>
- <span data-ttu-id="5aa11-383">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-383">'Identity'</span></span>
- <span data-ttu-id="5aa11-384">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-385">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-385">'Razor'</span></span>
- <span data-ttu-id="5aa11-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-388">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-388">'Blazor'</span></span>
- <span data-ttu-id="5aa11-389">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-389">'Identity'</span></span>
- <span data-ttu-id="5aa11-390">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-391">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-391">'Razor'</span></span>
- <span data-ttu-id="5aa11-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-394">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-394">'Blazor'</span></span>
- <span data-ttu-id="5aa11-395">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-395">'Identity'</span></span>
- <span data-ttu-id="5aa11-396">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-397">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-397">'Razor'</span></span>
- <span data-ttu-id="5aa11-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-400">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-400">'Blazor'</span></span>
- <span data-ttu-id="5aa11-401">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-401">'Identity'</span></span>
- <span data-ttu-id="5aa11-402">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-403">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-403">'Razor'</span></span>
- <span data-ttu-id="5aa11-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-406">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-406">'Blazor'</span></span>
- <span data-ttu-id="5aa11-407">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-407">'Identity'</span></span>
- <span data-ttu-id="5aa11-408">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-409">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-409">'Razor'</span></span>
- <span data-ttu-id="5aa11-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-412">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-412">'Blazor'</span></span>
- <span data-ttu-id="5aa11-413">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-413">'Identity'</span></span>
- <span data-ttu-id="5aa11-414">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-415">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-415">'Razor'</span></span>
- <span data-ttu-id="5aa11-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-416">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sí   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span><span class="sxs-lookup"><span data-stu-id="5aa11-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="5aa11-418">Después de la parte `^rewrite-rule/` de la expresión, hay dos grupos de captura, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="5aa11-419">`\d` significa *buscar un dígito (número)* .</span><span class="sxs-lookup"><span data-stu-id="5aa11-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="5aa11-420">El signo más (`+`) significa *buscar uno o más de los caracteres anteriores*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="5aa11-421">Por tanto, la URL debe contener un número seguido de una barra diagonal, seguida de otro número.</span><span class="sxs-lookup"><span data-stu-id="5aa11-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="5aa11-422">Estos grupos de capturas se insertan en la URL de reescritura como `$1` y `$2`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="5aa11-423">La cadena de reemplazo de la regla de reescritura coloca los grupos capturados en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="5aa11-424">La ruta de acceso solicitada de `/rewrite-rule/1234/5678` se reescribe para obtener el recurso en `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="5aa11-425">Si una cadena de consulta está presente en la solicitud original, se conserva cuando se reescribe la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="5aa11-426">No hay ningún recorrido de ida y vuelta al servidor para obtener el recurso.</span><span class="sxs-lookup"><span data-stu-id="5aa11-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="5aa11-427">Si el recurso existe, se captura y se devuelve al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="5aa11-428">Como el cliente no se redirige, la dirección URL no cambia en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="5aa11-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="5aa11-429">Los clientes no pueden detectar que se ha producido una operación de reescritura de URL en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5aa11-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5aa11-430">Use `skipRemainingRules: true` siempre que sea posible, ya que las reglas de coincidencia consumen muchos recursos y aumentan el tiempo de respuesta de aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="5aa11-431">Para obtener la respuesta más rápida de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="5aa11-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="5aa11-432">Ordene las reglas de reescritura desde la que coincida con más frecuencia a la que coincida con menos frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5aa11-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="5aa11-433">Omita el procesamiento de las reglas restantes cuando se produzca una coincidencia; no es necesario ningún procesamiento de reglas adicional.</span><span class="sxs-lookup"><span data-stu-id="5aa11-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="5aa11-434">mod_rewrite de Apache</span><span class="sxs-lookup"><span data-stu-id="5aa11-434">Apache mod_rewrite</span></span>

<span data-ttu-id="5aa11-435">Aplique reglas mod_rewrite de Apache con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="5aa11-436">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="5aa11-437">Para obtener más información y ejemplos de reglas mod_rewrite, vea [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache).</span><span class="sxs-lookup"><span data-stu-id="5aa11-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="5aa11-438">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="5aa11-439">La aplicación de ejemplo redirige las solicitudes de `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="5aa11-440">El código de estado de la respuesta es *302: Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="5aa11-441">Solicitud original: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="5aa11-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="5aa11-443">El middleware admite las siguientes variables de servidor mod_rewrite de Apache:</span><span class="sxs-lookup"><span data-stu-id="5aa11-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="5aa11-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="5aa11-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="5aa11-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="5aa11-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="5aa11-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="5aa11-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="5aa11-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="5aa11-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="5aa11-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="5aa11-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="5aa11-449">HTTP_HOST</span></span>
* <span data-ttu-id="5aa11-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="5aa11-450">HTTP_REFERER</span></span>
* <span data-ttu-id="5aa11-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="5aa11-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="5aa11-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="5aa11-452">HTTPS</span></span>
* <span data-ttu-id="5aa11-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="5aa11-453">IPV6</span></span>
* <span data-ttu-id="5aa11-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="5aa11-454">QUERY_STRING</span></span>
* <span data-ttu-id="5aa11-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="5aa11-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="5aa11-456">REMOTE_PORT</span></span>
* <span data-ttu-id="5aa11-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="5aa11-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="5aa11-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="5aa11-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="5aa11-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="5aa11-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="5aa11-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="5aa11-460">REQUEST_URI</span></span>
* <span data-ttu-id="5aa11-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="5aa11-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="5aa11-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-462">SERVER_ADDR</span></span>
* <span data-ttu-id="5aa11-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="5aa11-463">SERVER_PORT</span></span>
* <span data-ttu-id="5aa11-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="5aa11-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="5aa11-465">TIME</span><span class="sxs-lookup"><span data-stu-id="5aa11-465">TIME</span></span>
* <span data-ttu-id="5aa11-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="5aa11-466">TIME_DAY</span></span>
* <span data-ttu-id="5aa11-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="5aa11-467">TIME_HOUR</span></span>
* <span data-ttu-id="5aa11-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="5aa11-468">TIME_MIN</span></span>
* <span data-ttu-id="5aa11-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="5aa11-469">TIME_MON</span></span>
* <span data-ttu-id="5aa11-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="5aa11-470">TIME_SEC</span></span>
* <span data-ttu-id="5aa11-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="5aa11-471">TIME_WDAY</span></span>
* <span data-ttu-id="5aa11-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="5aa11-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="5aa11-473">Reglas del Módulo URL Rewrite para IIS</span><span class="sxs-lookup"><span data-stu-id="5aa11-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="5aa11-474">Para usar el mismo conjunto de reglas que se aplica al módulo URL Rewrite para IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="5aa11-475">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="5aa11-476">No dirija el middleware para que use el archivo *web.config* de la aplicación cuando se ejecute en Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="5aa11-477">Con IIS, estas reglas se deben almacenar fuera del archivo *web.config* de la aplicación para evitar conflictos con el Módulo URL Rewrite para IIS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="5aa11-478">Para obtener más información y ejemplos de reglas del Módulo URL Rewrite para IIS, vea [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0) y [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite).</span><span class="sxs-lookup"><span data-stu-id="5aa11-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="5aa11-479">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="5aa11-480">La aplicación de ejemplo reescribe las solicitudes de `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="5aa11-481">La respuesta se envía al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="5aa11-482">Solicitud original: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="5aa11-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="5aa11-484">Si tiene un Módulo URL Rewrite para IIS activo con reglas configuradas en el nivel de servidor que podrían afectar a la aplicación de manera no deseada, puede deshabilitar el Módulo URL Rewrite para IIS para una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="5aa11-485">Para más información, vea [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Deshabilitación de módulos de IIS).</span><span class="sxs-lookup"><span data-stu-id="5aa11-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="5aa11-486">Características no admitidas</span><span class="sxs-lookup"><span data-stu-id="5aa11-486">Unsupported features</span></span>

<span data-ttu-id="5aa11-487">El middleware publicado con ASP.NET Core 2.x no admite las siguientes características de Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="5aa11-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="5aa11-488">Reglas de salida</span><span class="sxs-lookup"><span data-stu-id="5aa11-488">Outbound Rules</span></span>
* <span data-ttu-id="5aa11-489">Variables de servidor personalizadas</span><span class="sxs-lookup"><span data-stu-id="5aa11-489">Custom Server Variables</span></span>
* <span data-ttu-id="5aa11-490">Caracteres comodín</span><span class="sxs-lookup"><span data-stu-id="5aa11-490">Wildcards</span></span>
* <span data-ttu-id="5aa11-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="5aa11-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="5aa11-492">Variables de servidor compatibles</span><span class="sxs-lookup"><span data-stu-id="5aa11-492">Supported server variables</span></span>

<span data-ttu-id="5aa11-493">El middleware admite las siguientes variables de servidor del Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="5aa11-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="5aa11-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="5aa11-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="5aa11-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="5aa11-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="5aa11-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="5aa11-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="5aa11-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="5aa11-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="5aa11-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="5aa11-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="5aa11-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="5aa11-499">HTTP_HOST</span></span>
* <span data-ttu-id="5aa11-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="5aa11-500">HTTP_REFERER</span></span>
* <span data-ttu-id="5aa11-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-501">HTTP_URL</span></span>
* <span data-ttu-id="5aa11-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="5aa11-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="5aa11-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="5aa11-503">HTTPS</span></span>
* <span data-ttu-id="5aa11-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="5aa11-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="5aa11-505">QUERY_STRING</span></span>
* <span data-ttu-id="5aa11-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="5aa11-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="5aa11-507">REMOTE_PORT</span></span>
* <span data-ttu-id="5aa11-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="5aa11-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="5aa11-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="5aa11-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="5aa11-510">También puede obtener <xref:Microsoft.Extensions.FileProviders.IFileProvider> a través de <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="5aa11-511">Con este enfoque logrará mayor flexibilidad para la ubicación de los archivos de reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="5aa11-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="5aa11-512">Asegúrese de que los archivos de reglas de reescritura se implementan en el servidor en la ruta de acceso que proporcione.</span><span class="sxs-lookup"><span data-stu-id="5aa11-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="5aa11-513">Regla basada en métodos</span><span class="sxs-lookup"><span data-stu-id="5aa11-513">Method-based rule</span></span>

<span data-ttu-id="5aa11-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar su propia lógica de la regla en un método.</span><span class="sxs-lookup"><span data-stu-id="5aa11-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="5aa11-515">`Add` expone el elemento <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, lo que hace que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible para usarlo en el método.</span><span class="sxs-lookup"><span data-stu-id="5aa11-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="5aa11-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina cómo se administra el procesamiento adicional en la canalización.</span><span class="sxs-lookup"><span data-stu-id="5aa11-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="5aa11-517">Establezca el valor en uno de los campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> que se describen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="5aa11-518">Acción</span><span class="sxs-lookup"><span data-stu-id="5aa11-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="5aa11-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-520">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-520">'Blazor'</span></span>
- <span data-ttu-id="5aa11-521">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-521">'Identity'</span></span>
- <span data-ttu-id="5aa11-522">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-523">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-523">'Razor'</span></span>
- <span data-ttu-id="5aa11-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-526">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-526">'Blazor'</span></span>
- <span data-ttu-id="5aa11-527">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-527">'Identity'</span></span>
- <span data-ttu-id="5aa11-528">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-529">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-529">'Razor'</span></span>
- <span data-ttu-id="5aa11-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-532">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-532">'Blazor'</span></span>
- <span data-ttu-id="5aa11-533">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-533">'Identity'</span></span>
- <span data-ttu-id="5aa11-534">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-535">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-535">'Razor'</span></span>
- <span data-ttu-id="5aa11-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-538">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-538">'Blazor'</span></span>
- <span data-ttu-id="5aa11-539">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-539">'Identity'</span></span>
- <span data-ttu-id="5aa11-540">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-541">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-541">'Razor'</span></span>
- <span data-ttu-id="5aa11-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-544">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-544">'Blazor'</span></span>
- <span data-ttu-id="5aa11-545">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-545">'Identity'</span></span>
- <span data-ttu-id="5aa11-546">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-547">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-547">'Razor'</span></span>
- <span data-ttu-id="5aa11-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-550">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-550">'Blazor'</span></span>
- <span data-ttu-id="5aa11-551">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-551">'Identity'</span></span>
- <span data-ttu-id="5aa11-552">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-553">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-553">'Razor'</span></span>
- <span data-ttu-id="5aa11-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-556">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-556">'Blazor'</span></span>
- <span data-ttu-id="5aa11-557">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-557">'Identity'</span></span>
- <span data-ttu-id="5aa11-558">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-559">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-559">'Razor'</span></span>
- <span data-ttu-id="5aa11-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-562">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-562">'Blazor'</span></span>
- <span data-ttu-id="5aa11-563">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-563">'Identity'</span></span>
- <span data-ttu-id="5aa11-564">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-565">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-565">'Razor'</span></span>
- <span data-ttu-id="5aa11-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-568">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-568">'Blazor'</span></span>
- <span data-ttu-id="5aa11-569">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-569">'Identity'</span></span>
- <span data-ttu-id="5aa11-570">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-571">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-571">'Razor'</span></span>
- <span data-ttu-id="5aa11-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-574">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-574">'Blazor'</span></span>
- <span data-ttu-id="5aa11-575">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-575">'Identity'</span></span>
- <span data-ttu-id="5aa11-576">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-577">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-577">'Razor'</span></span>
- <span data-ttu-id="5aa11-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-580">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-580">'Blazor'</span></span>
- <span data-ttu-id="5aa11-581">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-581">'Identity'</span></span>
- <span data-ttu-id="5aa11-582">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-583">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-583">'Razor'</span></span>
- <span data-ttu-id="5aa11-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-586">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-586">'Blazor'</span></span>
- <span data-ttu-id="5aa11-587">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-587">'Identity'</span></span>
- <span data-ttu-id="5aa11-588">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-589">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-589">'Razor'</span></span>
- <span data-ttu-id="5aa11-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-592">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-592">'Blazor'</span></span>
- <span data-ttu-id="5aa11-593">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-593">'Identity'</span></span>
- <span data-ttu-id="5aa11-594">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-595">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-595">'Razor'</span></span>
- <span data-ttu-id="5aa11-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-598">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-598">'Blazor'</span></span>
- <span data-ttu-id="5aa11-599">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-599">'Identity'</span></span>
- <span data-ttu-id="5aa11-600">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-601">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-601">'Razor'</span></span>
- <span data-ttu-id="5aa11-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-604">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-604">'Blazor'</span></span>
- <span data-ttu-id="5aa11-605">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-605">'Identity'</span></span>
- <span data-ttu-id="5aa11-606">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-607">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-607">'Razor'</span></span>
- <span data-ttu-id="5aa11-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-610">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-610">'Blazor'</span></span>
- <span data-ttu-id="5aa11-611">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-611">'Identity'</span></span>
- <span data-ttu-id="5aa11-612">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-613">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-613">'Razor'</span></span>
- <span data-ttu-id="5aa11-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-614">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-616">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-616">'Blazor'</span></span>
- <span data-ttu-id="5aa11-617">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-617">'Identity'</span></span>
- <span data-ttu-id="5aa11-618">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-619">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-619">'Razor'</span></span>
- <span data-ttu-id="5aa11-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-622">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-622">'Blazor'</span></span>
- <span data-ttu-id="5aa11-623">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-623">'Identity'</span></span>
- <span data-ttu-id="5aa11-624">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-625">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-625">'Razor'</span></span>
- <span data-ttu-id="5aa11-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-628">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-628">'Blazor'</span></span>
- <span data-ttu-id="5aa11-629">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-629">'Identity'</span></span>
- <span data-ttu-id="5aa11-630">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-631">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-631">'Razor'</span></span>
- <span data-ttu-id="5aa11-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-634">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-634">'Blazor'</span></span>
- <span data-ttu-id="5aa11-635">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-635">'Identity'</span></span>
- <span data-ttu-id="5aa11-636">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-637">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-637">'Razor'</span></span>
- <span data-ttu-id="5aa11-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-640">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-640">'Blazor'</span></span>
- <span data-ttu-id="5aa11-641">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-641">'Identity'</span></span>
- <span data-ttu-id="5aa11-642">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-643">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-643">'Razor'</span></span>
- <span data-ttu-id="5aa11-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-646">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-646">'Blazor'</span></span>
- <span data-ttu-id="5aa11-647">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-647">'Identity'</span></span>
- <span data-ttu-id="5aa11-648">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-649">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-649">'Razor'</span></span>
- <span data-ttu-id="5aa11-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-652">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-652">'Blazor'</span></span>
- <span data-ttu-id="5aa11-653">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-653">'Identity'</span></span>
- <span data-ttu-id="5aa11-654">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-655">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-655">'Razor'</span></span>
- <span data-ttu-id="5aa11-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-658">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-658">'Blazor'</span></span>
- <span data-ttu-id="5aa11-659">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-659">'Identity'</span></span>
- <span data-ttu-id="5aa11-660">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-661">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-661">'Razor'</span></span>
- <span data-ttu-id="5aa11-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-664">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-664">'Blazor'</span></span>
- <span data-ttu-id="5aa11-665">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-665">'Identity'</span></span>
- <span data-ttu-id="5aa11-666">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-667">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-667">'Razor'</span></span>
- <span data-ttu-id="5aa11-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-670">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-670">'Blazor'</span></span>
- <span data-ttu-id="5aa11-671">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-671">'Identity'</span></span>
- <span data-ttu-id="5aa11-672">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-673">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-673">'Razor'</span></span>
- <span data-ttu-id="5aa11-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-676">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-676">'Blazor'</span></span>
- <span data-ttu-id="5aa11-677">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-677">'Identity'</span></span>
- <span data-ttu-id="5aa11-678">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-679">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-679">'Razor'</span></span>
- <span data-ttu-id="5aa11-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-682">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-682">'Blazor'</span></span>
- <span data-ttu-id="5aa11-683">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-683">'Identity'</span></span>
- <span data-ttu-id="5aa11-684">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-685">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-685">'Razor'</span></span>
- <span data-ttu-id="5aa11-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-688">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-688">'Blazor'</span></span>
- <span data-ttu-id="5aa11-689">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-689">'Identity'</span></span>
- <span data-ttu-id="5aa11-690">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-691">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-691">'Razor'</span></span>
- <span data-ttu-id="5aa11-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-694">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-694">'Blazor'</span></span>
- <span data-ttu-id="5aa11-695">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-695">'Identity'</span></span>
- <span data-ttu-id="5aa11-696">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-697">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-697">'Razor'</span></span>
- <span data-ttu-id="5aa11-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-700">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-700">'Blazor'</span></span>
- <span data-ttu-id="5aa11-701">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-701">'Identity'</span></span>
- <span data-ttu-id="5aa11-702">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-703">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-703">'Razor'</span></span>
- <span data-ttu-id="5aa11-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-706">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-706">'Blazor'</span></span>
- <span data-ttu-id="5aa11-707">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-707">'Identity'</span></span>
- <span data-ttu-id="5aa11-708">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-709">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-709">'Razor'</span></span>
- <span data-ttu-id="5aa11-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-712">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-712">'Blazor'</span></span>
- <span data-ttu-id="5aa11-713">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-713">'Identity'</span></span>
- <span data-ttu-id="5aa11-714">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-715">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-715">'Razor'</span></span>
- <span data-ttu-id="5aa11-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-718">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-718">'Blazor'</span></span>
- <span data-ttu-id="5aa11-719">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-719">'Identity'</span></span>
- <span data-ttu-id="5aa11-720">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-721">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-721">'Razor'</span></span>
- <span data-ttu-id="5aa11-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-724">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-724">'Blazor'</span></span>
- <span data-ttu-id="5aa11-725">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-725">'Identity'</span></span>
- <span data-ttu-id="5aa11-726">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-727">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-727">'Razor'</span></span>
- <span data-ttu-id="5aa11-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-730">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-730">'Blazor'</span></span>
- <span data-ttu-id="5aa11-731">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-731">'Identity'</span></span>
- <span data-ttu-id="5aa11-732">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-733">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-733">'Razor'</span></span>
- <span data-ttu-id="5aa11-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-736">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-736">'Blazor'</span></span>
- <span data-ttu-id="5aa11-737">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-737">'Identity'</span></span>
- <span data-ttu-id="5aa11-738">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-739">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-739">'Razor'</span></span>
- <span data-ttu-id="5aa11-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-742">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-742">'Blazor'</span></span>
- <span data-ttu-id="5aa11-743">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-743">'Identity'</span></span>
- <span data-ttu-id="5aa11-744">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-745">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-745">'Razor'</span></span>
- <span data-ttu-id="5aa11-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-748">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-748">'Blazor'</span></span>
- <span data-ttu-id="5aa11-749">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-749">'Identity'</span></span>
- <span data-ttu-id="5aa11-750">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-751">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-751">'Razor'</span></span>
- <span data-ttu-id="5aa11-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-754">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-754">'Blazor'</span></span>
- <span data-ttu-id="5aa11-755">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-755">'Identity'</span></span>
- <span data-ttu-id="5aa11-756">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-757">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-757">'Razor'</span></span>
- <span data-ttu-id="5aa11-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-760">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-760">'Blazor'</span></span>
- <span data-ttu-id="5aa11-761">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-761">'Identity'</span></span>
- <span data-ttu-id="5aa11-762">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-763">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-763">'Razor'</span></span>
- <span data-ttu-id="5aa11-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-766">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-766">'Blazor'</span></span>
- <span data-ttu-id="5aa11-767">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-767">'Identity'</span></span>
- <span data-ttu-id="5aa11-768">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-769">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-769">'Razor'</span></span>
- <span data-ttu-id="5aa11-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-772">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-772">'Blazor'</span></span>
- <span data-ttu-id="5aa11-773">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-773">'Identity'</span></span>
- <span data-ttu-id="5aa11-774">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-775">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-775">'Razor'</span></span>
- <span data-ttu-id="5aa11-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-778">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-778">'Blazor'</span></span>
- <span data-ttu-id="5aa11-779">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-779">'Identity'</span></span>
- <span data-ttu-id="5aa11-780">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-781">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-781">'Razor'</span></span>
- <span data-ttu-id="5aa11-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-784">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-784">'Blazor'</span></span>
- <span data-ttu-id="5aa11-785">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-785">'Identity'</span></span>
- <span data-ttu-id="5aa11-786">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-787">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-787">'Razor'</span></span>
- <span data-ttu-id="5aa11-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-790">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-790">'Blazor'</span></span>
- <span data-ttu-id="5aa11-791">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-791">'Identity'</span></span>
- <span data-ttu-id="5aa11-792">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-793">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-793">'Razor'</span></span>
- <span data-ttu-id="5aa11-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-794">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-795">-------------------------------- | | `RuleResult.ContinueRules` (valor predeterminado) | Continuar aplicando las reglas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="5aa11-796">| | `RuleResult.EndResponse`             | Dejar de aplicar las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="5aa11-797">| | `RuleResult.SkipRemainingRules`      | Dejar de aplicar las reglas y enviar el contexto al middleware siguiente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="5aa11-798">La aplicación de ejemplo muestra un método que redirige las solicitudes para las rutas de acceso que terminen con *.xml*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="5aa11-799">Si se realiza una solicitud de `/file.xml`, la solicitud se redirige a `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="5aa11-800">El código de estado se establece en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="5aa11-801">Cuando el explorador realiza una solicitud nueva a */xmlfiles/file.xml*, el middleware de archivos estáticos sirve el archivo al cliente desde la carpeta *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="5aa11-802">Para un redireccionamiento, debe establecer de forma explícita el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="5aa11-803">En caso contrario, se devuelve un código de estado *200: correcto* y no se produce el redireccionamiento en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="5aa11-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="5aa11-805">Este enfoque también permite volver a escribir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="5aa11-806">En la aplicación de ejemplo se muestra cómo volver a escribir la ruta de acceso para cualquier solicitud de archivo de texto para proporcionar el archivo de texto *file.txt* desde la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="5aa11-807">El middleware de archivos estáticos proporciona el archivo en función de la ruta de acceso de la solicitud actualizada:</span><span class="sxs-lookup"><span data-stu-id="5aa11-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="5aa11-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="5aa11-809">Regla basada en IRule</span><span class="sxs-lookup"><span data-stu-id="5aa11-809">IRule-based rule</span></span>

<span data-ttu-id="5aa11-810">Utilice <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar la lógica de reglas en una clase que implemente la interfaz <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="5aa11-811">`IRule` proporciona mayor flexibilidad que si se usa el enfoque de reglas basadas en métodos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="5aa11-812">La clase de implementación puede incluir un constructor que permita pasar parámetros para el método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="5aa11-813">Se comprueba que los valores de los parámetros en la aplicación de ejemplo para `extension` y `newPath` cumplen ciertas condiciones.</span><span class="sxs-lookup"><span data-stu-id="5aa11-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="5aa11-814">`extension` debe contener un valor, que debe ser *.png*, *.jpg* o *.gif*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="5aa11-815">Si `newPath` no es válido, se genera <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="5aa11-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="5aa11-816">Si se realiza una solicitud de *image.png*, la solicitud se redirige a `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="5aa11-817">Si se realiza una solicitud de *image.jpg*, la solicitud se redirige a `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="5aa11-818">El código de estado se establece en *301 - Movido definitivamente* y se establece `context.Result` para detener el procesamiento de las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="5aa11-819">Solicitud original: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="5aa11-819">Original Request: `/image.png`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="5aa11-821">Solicitud original: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="5aa11-821">Original Request: `/image.jpg`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="5aa11-823">Ejemplos de expresiones regulares</span><span class="sxs-lookup"><span data-stu-id="5aa11-823">Regex examples</span></span>

| <span data-ttu-id="5aa11-824">Objetivo</span><span class="sxs-lookup"><span data-stu-id="5aa11-824">Goal</span></span> | <span data-ttu-id="5aa11-825">Cadena de expresión regular &</span><span class="sxs-lookup"><span data-stu-id="5aa11-825">Regex String &</span></span><br><span data-ttu-id="5aa11-826">Ejemplo de coincidencia</span><span class="sxs-lookup"><span data-stu-id="5aa11-826">Match Example</span></span> | <span data-ttu-id="5aa11-827">Cadena de reemplazo &</span><span class="sxs-lookup"><span data-stu-id="5aa11-827">Replacement String &</span></span><br><span data-ttu-id="5aa11-828">Ejemplo de resultado</span><span class="sxs-lookup"><span data-stu-id="5aa11-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="5aa11-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-830">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-830">'Blazor'</span></span>
- <span data-ttu-id="5aa11-831">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-831">'Identity'</span></span>
- <span data-ttu-id="5aa11-832">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-833">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-833">'Razor'</span></span>
- <span data-ttu-id="5aa11-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-836">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-836">'Blazor'</span></span>
- <span data-ttu-id="5aa11-837">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-837">'Identity'</span></span>
- <span data-ttu-id="5aa11-838">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-839">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-839">'Razor'</span></span>
- <span data-ttu-id="5aa11-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-842">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-842">'Blazor'</span></span>
- <span data-ttu-id="5aa11-843">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-843">'Identity'</span></span>
- <span data-ttu-id="5aa11-844">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-845">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-845">'Razor'</span></span>
- <span data-ttu-id="5aa11-846">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-848">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-848">'Blazor'</span></span>
- <span data-ttu-id="5aa11-849">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-849">'Identity'</span></span>
- <span data-ttu-id="5aa11-850">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-851">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-851">'Razor'</span></span>
- <span data-ttu-id="5aa11-852">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-854">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-854">'Blazor'</span></span>
- <span data-ttu-id="5aa11-855">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-855">'Identity'</span></span>
- <span data-ttu-id="5aa11-856">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-857">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-857">'Razor'</span></span>
- <span data-ttu-id="5aa11-858">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-860">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-860">'Blazor'</span></span>
- <span data-ttu-id="5aa11-861">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-861">'Identity'</span></span>
- <span data-ttu-id="5aa11-862">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-863">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-863">'Razor'</span></span>
- <span data-ttu-id="5aa11-864">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-866">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-866">'Blazor'</span></span>
- <span data-ttu-id="5aa11-867">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-867">'Identity'</span></span>
- <span data-ttu-id="5aa11-868">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-869">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-869">'Razor'</span></span>
- <span data-ttu-id="5aa11-870">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-872">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-872">'Blazor'</span></span>
- <span data-ttu-id="5aa11-873">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-873">'Identity'</span></span>
- <span data-ttu-id="5aa11-874">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-875">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-875">'Razor'</span></span>
- <span data-ttu-id="5aa11-876">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-878">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-878">'Blazor'</span></span>
- <span data-ttu-id="5aa11-879">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-879">'Identity'</span></span>
- <span data-ttu-id="5aa11-880">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-881">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-881">'Razor'</span></span>
- <span data-ttu-id="5aa11-882">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-884">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-884">'Blazor'</span></span>
- <span data-ttu-id="5aa11-885">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-885">'Identity'</span></span>
- <span data-ttu-id="5aa11-886">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-887">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-887">'Razor'</span></span>
- <span data-ttu-id="5aa11-888">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-890">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-890">'Blazor'</span></span>
- <span data-ttu-id="5aa11-891">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-891">'Identity'</span></span>
- <span data-ttu-id="5aa11-892">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-893">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-893">'Razor'</span></span>
- <span data-ttu-id="5aa11-894">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-896">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-896">'Blazor'</span></span>
- <span data-ttu-id="5aa11-897">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-897">'Identity'</span></span>
- <span data-ttu-id="5aa11-898">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-899">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-899">'Razor'</span></span>
- <span data-ttu-id="5aa11-900">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-902">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-902">'Blazor'</span></span>
- <span data-ttu-id="5aa11-903">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-903">'Identity'</span></span>
- <span data-ttu-id="5aa11-904">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-905">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-905">'Razor'</span></span>
- <span data-ttu-id="5aa11-906">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-906">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-908">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-908">'Blazor'</span></span>
- <span data-ttu-id="5aa11-909">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-909">'Identity'</span></span>
- <span data-ttu-id="5aa11-910">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-911">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-911">'Razor'</span></span>
- <span data-ttu-id="5aa11-912">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-914">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-914">'Blazor'</span></span>
- <span data-ttu-id="5aa11-915">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-915">'Identity'</span></span>
- <span data-ttu-id="5aa11-916">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-917">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-917">'Razor'</span></span>
- <span data-ttu-id="5aa11-918">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-920">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-920">'Blazor'</span></span>
- <span data-ttu-id="5aa11-921">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-921">'Identity'</span></span>
- <span data-ttu-id="5aa11-922">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-923">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-923">'Razor'</span></span>
- <span data-ttu-id="5aa11-924">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-926">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-926">'Blazor'</span></span>
- <span data-ttu-id="5aa11-927">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-927">'Identity'</span></span>
- <span data-ttu-id="5aa11-928">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-929">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-929">'Razor'</span></span>
- <span data-ttu-id="5aa11-930">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-932">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-932">'Blazor'</span></span>
- <span data-ttu-id="5aa11-933">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-933">'Identity'</span></span>
- <span data-ttu-id="5aa11-934">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-935">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-935">'Razor'</span></span>
- <span data-ttu-id="5aa11-936">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-938">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-938">'Blazor'</span></span>
- <span data-ttu-id="5aa11-939">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-939">'Identity'</span></span>
- <span data-ttu-id="5aa11-940">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-941">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-941">'Razor'</span></span>
- <span data-ttu-id="5aa11-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-944">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-944">'Blazor'</span></span>
- <span data-ttu-id="5aa11-945">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-945">'Identity'</span></span>
- <span data-ttu-id="5aa11-946">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-947">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-947">'Razor'</span></span>
- <span data-ttu-id="5aa11-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-950">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-950">'Blazor'</span></span>
- <span data-ttu-id="5aa11-951">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-951">'Identity'</span></span>
- <span data-ttu-id="5aa11-952">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-953">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-953">'Razor'</span></span>
- <span data-ttu-id="5aa11-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-956">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-956">'Blazor'</span></span>
- <span data-ttu-id="5aa11-957">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-957">'Identity'</span></span>
- <span data-ttu-id="5aa11-958">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-959">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-959">'Razor'</span></span>
- <span data-ttu-id="5aa11-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-962">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-962">'Blazor'</span></span>
- <span data-ttu-id="5aa11-963">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-963">'Identity'</span></span>
- <span data-ttu-id="5aa11-964">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-965">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-965">'Razor'</span></span>
- <span data-ttu-id="5aa11-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-968">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-968">'Blazor'</span></span>
- <span data-ttu-id="5aa11-969">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-969">'Identity'</span></span>
- <span data-ttu-id="5aa11-970">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-971">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-971">'Razor'</span></span>
- <span data-ttu-id="5aa11-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-974">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-974">'Blazor'</span></span>
- <span data-ttu-id="5aa11-975">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-975">'Identity'</span></span>
- <span data-ttu-id="5aa11-976">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-977">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-977">'Razor'</span></span>
- <span data-ttu-id="5aa11-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-980">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-980">'Blazor'</span></span>
- <span data-ttu-id="5aa11-981">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-981">'Identity'</span></span>
- <span data-ttu-id="5aa11-982">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-983">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-983">'Razor'</span></span>
- <span data-ttu-id="5aa11-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-986">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-986">'Blazor'</span></span>
- <span data-ttu-id="5aa11-987">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-987">'Identity'</span></span>
- <span data-ttu-id="5aa11-988">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-989">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-989">'Razor'</span></span>
- <span data-ttu-id="5aa11-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-992">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-992">'Blazor'</span></span>
- <span data-ttu-id="5aa11-993">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-993">'Identity'</span></span>
- <span data-ttu-id="5aa11-994">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-995">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-995">'Razor'</span></span>
- <span data-ttu-id="5aa11-996">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-998">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-998">'Blazor'</span></span>
- <span data-ttu-id="5aa11-999">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-999">'Identity'</span></span>
- <span data-ttu-id="5aa11-1000">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1001">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1001">'Razor'</span></span>
- <span data-ttu-id="5aa11-1002">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1004">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1004">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1005">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1005">'Identity'</span></span>
- <span data-ttu-id="5aa11-1006">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1007">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1007">'Razor'</span></span>
- <span data-ttu-id="5aa11-1008">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1009">------------------- | | Ruta de acceso de reescritura en la cadena de consulta | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="5aa11-1010">`/path?var1=abc&var2=123` | | Quitar barra diagonal final | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="5aa11-1011">`/path` | | Exigir barra diagonal final | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="5aa11-1012">`/path/` | | Evitar reescritura de solicitudes específicas | `^(.*)(?<!\.axd)$` o `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="5aa11-1013">Sí: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="5aa11-1014">No: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="5aa11-1015">`/resource.axd` | | Reorganizar los segmentos de URL | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="5aa11-1016">`path/3/2/1` | | Reemplazar un segmento de URL | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5aa11-1017">En este documento se ofrece una introducción a la reescritura de URL e instrucciones sobre cómo usar el middleware de reescritura de URL en aplicaciones ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="5aa11-1018">La reescritura de URL consiste en modificar varias URL de solicitud basadas en una o varias reglas predefinidas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="5aa11-1019">La reescritura de URL crea una abstracción entre las ubicaciones de recursos y sus direcciones para que las ubicaciones y las direcciones no estén estrechamente vinculadas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="5aa11-1020">La reescritura de URL es útil en varios escenarios para:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="5aa11-1021">Mover o reemplazar recursos del servidor de forma temporal o permanente a la vez que se mantienen localizadores estables para esos recursos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="5aa11-1022">Dividir el procesamiento de solicitudes entre otras aplicaciones o entre áreas de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="5aa11-1023">Quitar, agregar o reorganizar segmentos de dirección URL en solicitudes entrantes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="5aa11-1024">Optimizar direcciones URL públicas para la optimización del motor de búsqueda (SEO).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="5aa11-1025">Permitir el uso de direcciones URL públicas descriptivas para ayudar a los visitantes a predecir el contenido devuelto mediante la solicitud de un recurso.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="5aa11-1026">Redirigir solicitudes no seguras a puntos de conexión seguros.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="5aa11-1027">Evitar la vinculación activa, en la que un sitio externo usa un recurso estático hospedado en otro sitio mediante la vinculación del recurso a su propio contenido.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="5aa11-1028">La reescritura de URL puede reducir el rendimiento de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="5aa11-1029">Cuando sea factible, limite el número y la complejidad de las reglas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="5aa11-1030">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5aa11-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="5aa11-1031">Redireccionamiento y reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="5aa11-1032">La diferencia entre los términos *redirección de URL* y *reescritura de URL* es sutil, pero tiene importantes implicaciones para proporcionar recursos a los clientes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="5aa11-1033">El middleware de reescritura de URL de ASP.NET Core es capaz de satisfacer las necesidades de ambos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="5aa11-1034">Una *redirección de URL* implica una operación del lado cliente, donde al cliente se le indica que acceda a un recurso en una dirección distinta a la que ha solicitado originalmente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="5aa11-1035">Esto requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="5aa11-1036">La URL de redireccionamiento que se devuelve al cliente aparece en la barra de direcciones del explorador cuando el cliente realiza una nueva solicitud para el recurso.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="5aa11-1037">Si `/resource` se *redirige* a `/different-resource`, el servidor responde que el cliente debe obtener el recurso en `/different-resource` con un código de estado que indica que la redirección es temporal o permanente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Se cambió temporalmente un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="5aa11-1043">Al redirigir las solicitudes a otra dirección URL, se indica si la redirección es permanente o temporal mediante la especificación del código de estado con la respuesta:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="5aa11-1044">El código de estado *301 - Movido definitivamente* se usa cuando el recurso tiene una nueva dirección URL permanente y se quiere indicar al cliente que todas las solicitudes futuras para el recurso deben usar la nueva dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="5aa11-1045">*El cliente puede almacenar en caché la respuesta cuando se recibe un código de estado 301.*</span><span class="sxs-lookup"><span data-stu-id="5aa11-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="5aa11-1046">El código de estado *302: encontrado* se usa cuando el redireccionamiento es temporal o en general está sujeto a cambios.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="5aa11-1047">El código de estado 302 indica al cliente que no almacene la dirección URL y la use en el futuro.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="5aa11-1048">Para obtener más información sobre los códigos de estado, consulte [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: definiciones de los códigos de estado).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="5aa11-1049">La *reescritura de URL* es una operación del lado servidor que proporciona un recurso desde una dirección de recursos distinta a la que el cliente ha solicitado.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="5aa11-1050">La reescritura de una dirección URL no requiere un recorrido de ida y vuelta al servidor.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="5aa11-1051">La dirección URL reescrita no se devuelve al cliente y no aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="5aa11-1052">Si `/resource` se *reescribe* como `/different-resource`, el servidor la obtiene *internamente* y devuelve el recurso en `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="5aa11-1053">Aunque es posible que el cliente pueda recuperar el recurso en la dirección URL reescrita, no se le informa de que el recurso existe en la dirección URL reescrita cuando realiza su solicitud y recibe la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Se cambió un punto de conexión del servicio WebAPI de la versión 1 (v1) a la versión 2 (v2) en el servidor.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="5aa11-1058">Aplicación de ejemplo de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1058">URL rewriting sample app</span></span>

<span data-ttu-id="5aa11-1059">Puede explorar las características del middleware de reescritura de URL con la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="5aa11-1060">La aplicación emplea las reglas de redireccionamiento y reescritura, y muestra la URL redirigida o reescrita para varios escenarios.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="5aa11-1061">Cuándo usar el middleware de reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="5aa11-1062">Use el middleware de reescritura de URL cuando no pueda usar los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="5aa11-1063">Módulo de reescritura de URL con IIS en Windows Server</span><span class="sxs-lookup"><span data-stu-id="5aa11-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="5aa11-1064">Módulo mod_rewrite de Apache en el servidor Apache</span><span class="sxs-lookup"><span data-stu-id="5aa11-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="5aa11-1065">Reescritura de URL en Nginx</span><span class="sxs-lookup"><span data-stu-id="5aa11-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="5aa11-1066">Además, use el middleware cuando la aplicación se hospede en el [servidor HTTP.sys](xref:fundamentals/servers/httpsys) (anteriormente denominado WebListener).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="5aa11-1067">Las principales razones para usar la tecnologías de reescritura de URL basadas en servidor en IIS, Apache y Nginx son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="5aa11-1068">El middleware no es compatible con todas las características de estos módulos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="5aa11-1069">Algunas de las características de los módulos de servidor no funcionan con proyectos de ASP.NET Core, como las restricciones `IsFile` y `IsDirectory` del módulo de reescritura de IIS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="5aa11-1070">En estos casos, es mejor usar el middleware.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="5aa11-1071">El rendimiento del middleware probablemente no coincida con el de los módulos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="5aa11-1072">La única manera de saber con certeza con qué enfoque el rendimiento disminuye más, o si la disminución de este es insignificante, es mediante una comparación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="5aa11-1073">Package</span><span class="sxs-lookup"><span data-stu-id="5aa11-1073">Package</span></span>

<span data-ttu-id="5aa11-1074">Para incluir el middleware en el proyecto, agregue una referencia de paquete al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) del archivo de proyecto, que contiene el paquete [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="5aa11-1075">Si no se usa el metapaquete `Microsoft.AspNetCore.App`, agregue una referencia de proyecto al paquete `Microsoft.AspNetCore.Rewrite`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="5aa11-1076">Extensión y opciones</span><span class="sxs-lookup"><span data-stu-id="5aa11-1076">Extension and options</span></span>

<span data-ttu-id="5aa11-1077">Establezca las reglas de reescritura y redirección de URL mediante la creación de una instancia de la clase [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con métodos de extensión para cada una de las reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="5aa11-1078">Encadene varias reglas en el orden que quiera procesarlas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="5aa11-1079">Las `RewriteOptions` se pasan al middleware de reescritura de URL cuando se agrega a la canalización de solicitudes con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="5aa11-1080">Redirigir solicitudes que distintas de www a www</span><span class="sxs-lookup"><span data-stu-id="5aa11-1080">Redirect non-www to www</span></span>

<span data-ttu-id="5aa11-1081">Hay tres opciones que permiten a la aplicación redirigir solicitudes distintas de `www` a `www`:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="5aa11-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: redirige permanentemente la solicitud al subdominio `www` si la solicitud es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="5aa11-1083">Se redirige con un código de estado [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="5aa11-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: redirige la solicitud al subdominio `www` si la solicitud de entrada es distinta de `www`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="5aa11-1085">Se redirige con un código de estado [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="5aa11-1086">Una sobrecarga permite proporcionar el código de estado para la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="5aa11-1087">Use un campo de la clase <xref:Microsoft.AspNetCore.Http.StatusCodes> para una asignación de código de estado.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="5aa11-1088">Redirección de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1088">URL redirect</span></span>

<span data-ttu-id="5aa11-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> para redirigir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="5aa11-1090">El primer parámetro contiene la expresión regular para hacer coincidir la ruta de acceso de la URL entrante.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="5aa11-1091">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="5aa11-1092">El tercer parámetro, si está presente, especifica el código de estado.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="5aa11-1093">Si no se especifica el código de estado, el valor predeterminado es *302 - Encontrado*, lo que indica que el recurso se ha movido o reemplazado temporalmente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="5aa11-1094">En un explorador con herramientas de desarrollo habilitadas, realice una solicitud a la aplicación de ejemplo con la ruta de acceso `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="5aa11-1095">La expresión regular coincide con la ruta de acceso de la solicitud en `redirect-rule/(.*)` y la ruta de acceso se reemplaza con `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="5aa11-1096">La dirección URL de redireccionamiento se devuelve al cliente con un código de estado *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="5aa11-1097">El explorador realiza una solicitud nueva en la URL de redireccionamiento, que aparece en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="5aa11-1098">Como ninguna de las reglas de la aplicación de ejemplo coincide con la dirección URL de redireccionamiento:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="5aa11-1099">La segunda solicitud recibe una respuesta *200: correcto* de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="5aa11-1100">En el cuerpo de la respuesta se muestra la dirección URL de redireccionamiento.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="5aa11-1101">Se realiza un recorrido de ida y vuelta al servidor cuando se *redirige* una dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="5aa11-1102">Tenga cuidado al establecer las reglas de redirección.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="5aa11-1103">Las reglas de redirección se evalúan en cada solicitud enviada a la aplicación, incluso después de una redirección.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="5aa11-1104">Es fácil crear por error un *bucle de redirecciones infinitas*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="5aa11-1105">Solicitud original: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="5aa11-1107">La parte de la expresión incluida entre paréntesis se denomina *grupo de capturas*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="5aa11-1108">El punto (`.`) de la expresión significa *buscar cualquier carácter*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="5aa11-1109">El asterisco (`*`) indica *buscar el carácter precedente cero o más veces*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="5aa11-1110">Por tanto, el grupo de capturas `(.*)` busca los dos últimos segmentos de la ruta de acceso de la URL, `1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="5aa11-1111">Cualquier valor que se proporcione en la URL de la solicitud después de `redirect-rule/` es capturado por este grupo de capturas único.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="5aa11-1112">En la cadena de reemplazo, se insertan los grupos capturados en la cadena con el signo de dólar (`$`) seguido del número de secuencia de la captura.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="5aa11-1113">Se obtiene el valor del primer grupo de capturas con `$1`, el segundo con `$2`, y así siguen en secuencia para los grupos de capturas de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="5aa11-1114">Solo hay un grupo capturado en la expresión regular de la regla de redirección de la aplicación de ejemplo, por lo que solo hay un grupo insertado en la cadena de reemplazo, que es `$1`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="5aa11-1115">Cuando se aplica la regla, la URL se convierte en `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="5aa11-1116">Redirección de URL a un punto de conexión segura</span><span class="sxs-lookup"><span data-stu-id="5aa11-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="5aa11-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> para redirigir las solicitudes HTTP al mismo host y ruta de acceso mediante el protocolo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="5aa11-1118">Si no se proporciona el código de estado, el middleware muestra de forma predeterminada *302 - Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="5aa11-1119">Si no se proporciona el puerto:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="5aa11-1120">El middleware tiene como valor predeterminado `null`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="5aa11-1121">El esquema cambia a `https` (protocolo HTTPS), y el cliente accede al recurso en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="5aa11-1122">En el ejemplo siguiente se muestra cómo establecer el código de estado en *301 - Movido definitivamente* y cambiar el puerto a 5001.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="5aa11-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> para redirigir las solicitudes no seguras al mismo host y ruta de acceso mediante el protocolo HTTPS seguro en el puerto 443.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="5aa11-1124">El middleware establece el código de estado en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="5aa11-1125">Al redirigir a un punto de conexión seguro sin la necesidad de reglas de redirección adicionales, se recomienda usar el Middleware de redirección de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="5aa11-1126">Para más información, vea el tema [Aplicación de HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="5aa11-1127">La aplicación de ejemplo es capaz de mostrar cómo usar `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="5aa11-1128">Agregue el método de extensión a `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="5aa11-1129">Realice una solicitud poco segura a la aplicación en cualquier URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="5aa11-1130">Descarte la advertencia de seguridad del explorador que indica que el certificado autofirmado no es de confianza o cree una excepción para confiar en el certificado en cuestión.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="5aa11-1131">Solicitud original mediante `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="5aa11-1133">Solicitud original mediante `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="5aa11-1135">Reescritura de URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1135">URL rewrite</span></span>

<span data-ttu-id="5aa11-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> para crear una regla para reescribir URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="5aa11-1137">El primer parámetro contiene la expresión regular para buscar coincidencias en la ruta de dirección de URL entrante.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="5aa11-1138">El segundo parámetro es la cadena de reemplazo.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="5aa11-1139">El tercer parámetro, `skipRemainingRules: {true|false}`, indica al middleware si al aplicar la regla actual tiene que omitir o no alguna regla de redirección adicional.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="5aa11-1140">Solicitud original: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="5aa11-1142">El acento circunflejo (`^`) al principio de la expresión significa que la búsqueda de coincidencias empieza al principio de la ruta de dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="5aa11-1143">En el ejemplo anterior de la regla de redireccionamiento (`redirect-rule/(.*)`), no hay ningún acento circunflejo (`^`) al principio de la expresión regular.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="5aa11-1144">Por tanto, cualquier carácter puede preceder a `redirect-rule/` en la ruta de acceso para que la coincidencia sea correcta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="5aa11-1145">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="5aa11-1145">Path</span></span>                               | <span data-ttu-id="5aa11-1146">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="5aa11-1146">Match</span></span> |
| ---
<span data-ttu-id="5aa11-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1148">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1148">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1149">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1149">'Identity'</span></span>
- <span data-ttu-id="5aa11-1150">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1151">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1151">'Razor'</span></span>
- <span data-ttu-id="5aa11-1152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1154">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1154">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1155">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1155">'Identity'</span></span>
- <span data-ttu-id="5aa11-1156">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1157">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1157">'Razor'</span></span>
- <span data-ttu-id="5aa11-1158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1160">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1160">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1161">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1161">'Identity'</span></span>
- <span data-ttu-id="5aa11-1162">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1163">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1163">'Razor'</span></span>
- <span data-ttu-id="5aa11-1164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1166">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1166">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1167">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1167">'Identity'</span></span>
- <span data-ttu-id="5aa11-1168">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1169">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1169">'Razor'</span></span>
- <span data-ttu-id="5aa11-1170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1172">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1172">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1173">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1173">'Identity'</span></span>
- <span data-ttu-id="5aa11-1174">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1175">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1175">'Razor'</span></span>
- <span data-ttu-id="5aa11-1176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1178">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1178">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1179">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1179">'Identity'</span></span>
- <span data-ttu-id="5aa11-1180">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1181">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1181">'Razor'</span></span>
- <span data-ttu-id="5aa11-1182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1184">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1184">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1185">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1185">'Identity'</span></span>
- <span data-ttu-id="5aa11-1186">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1187">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1187">'Razor'</span></span>
- <span data-ttu-id="5aa11-1188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1190">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1190">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1191">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1191">'Identity'</span></span>
- <span data-ttu-id="5aa11-1192">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1193">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1193">'Razor'</span></span>
- <span data-ttu-id="5aa11-1194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1196">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1196">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1197">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1197">'Identity'</span></span>
- <span data-ttu-id="5aa11-1198">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1199">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1199">'Razor'</span></span>
- <span data-ttu-id="5aa11-1200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1202">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1202">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1203">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1203">'Identity'</span></span>
- <span data-ttu-id="5aa11-1204">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1205">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1205">'Razor'</span></span>
- <span data-ttu-id="5aa11-1206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1208">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1208">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1209">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1209">'Identity'</span></span>
- <span data-ttu-id="5aa11-1210">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1211">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1211">'Razor'</span></span>
- <span data-ttu-id="5aa11-1212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1214">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1214">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1215">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1215">'Identity'</span></span>
- <span data-ttu-id="5aa11-1216">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1217">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1217">'Razor'</span></span>
- <span data-ttu-id="5aa11-1218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1220">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1220">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1221">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1221">'Identity'</span></span>
- <span data-ttu-id="5aa11-1222">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1223">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1223">'Razor'</span></span>
- <span data-ttu-id="5aa11-1224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1226">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1226">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1227">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1227">'Identity'</span></span>
- <span data-ttu-id="5aa11-1228">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1229">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1229">'Razor'</span></span>
- <span data-ttu-id="5aa11-1230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1232">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1232">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1233">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1233">'Identity'</span></span>
- <span data-ttu-id="5aa11-1234">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1235">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1235">'Razor'</span></span>
- <span data-ttu-id="5aa11-1236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sí   | | `/my-cool-redirect-rule/1234/5678` | Sí   | | `/anotherredirect-rule/1234/5678`  | Sí   |</span><span class="sxs-lookup"><span data-stu-id="5aa11-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="5aa11-1238">La regla de reescritura, `^rewrite-rule/(\d+)/(\d+)`, solo encuentra rutas de acceso que empiezan con `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="5aa11-1239">En la tabla siguiente, observe la diferencia de coincidencia.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="5aa11-1240">Ruta de acceso</span><span class="sxs-lookup"><span data-stu-id="5aa11-1240">Path</span></span>                              | <span data-ttu-id="5aa11-1241">Coincidir con</span><span class="sxs-lookup"><span data-stu-id="5aa11-1241">Match</span></span> |
| ---
<span data-ttu-id="5aa11-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1243">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1243">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1244">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1244">'Identity'</span></span>
- <span data-ttu-id="5aa11-1245">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1246">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1246">'Razor'</span></span>
- <span data-ttu-id="5aa11-1247">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1249">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1249">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1250">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1250">'Identity'</span></span>
- <span data-ttu-id="5aa11-1251">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1252">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1252">'Razor'</span></span>
- <span data-ttu-id="5aa11-1253">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1255">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1255">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1256">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1256">'Identity'</span></span>
- <span data-ttu-id="5aa11-1257">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1258">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1258">'Razor'</span></span>
- <span data-ttu-id="5aa11-1259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1261">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1261">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1262">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1262">'Identity'</span></span>
- <span data-ttu-id="5aa11-1263">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1264">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1264">'Razor'</span></span>
- <span data-ttu-id="5aa11-1265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1267">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1267">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1268">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1268">'Identity'</span></span>
- <span data-ttu-id="5aa11-1269">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1270">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1270">'Razor'</span></span>
- <span data-ttu-id="5aa11-1271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1273">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1273">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1274">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1274">'Identity'</span></span>
- <span data-ttu-id="5aa11-1275">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1276">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1276">'Razor'</span></span>
- <span data-ttu-id="5aa11-1277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1279">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1279">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1280">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1280">'Identity'</span></span>
- <span data-ttu-id="5aa11-1281">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1282">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1282">'Razor'</span></span>
- <span data-ttu-id="5aa11-1283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1285">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1285">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1286">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1286">'Identity'</span></span>
- <span data-ttu-id="5aa11-1287">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1288">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1288">'Razor'</span></span>
- <span data-ttu-id="5aa11-1289">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1291">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1291">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1292">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1292">'Identity'</span></span>
- <span data-ttu-id="5aa11-1293">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1294">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1294">'Razor'</span></span>
- <span data-ttu-id="5aa11-1295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1297">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1297">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1298">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1298">'Identity'</span></span>
- <span data-ttu-id="5aa11-1299">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1300">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1300">'Razor'</span></span>
- <span data-ttu-id="5aa11-1301">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1303">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1303">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1304">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1304">'Identity'</span></span>
- <span data-ttu-id="5aa11-1305">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1306">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1306">'Razor'</span></span>
- <span data-ttu-id="5aa11-1307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1309">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1309">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1310">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1310">'Identity'</span></span>
- <span data-ttu-id="5aa11-1311">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1312">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1312">'Razor'</span></span>
- <span data-ttu-id="5aa11-1313">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1315">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1315">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1316">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1316">'Identity'</span></span>
- <span data-ttu-id="5aa11-1317">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1318">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1318">'Razor'</span></span>
- <span data-ttu-id="5aa11-1319">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1321">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1321">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1322">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1322">'Identity'</span></span>
- <span data-ttu-id="5aa11-1323">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1324">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1324">'Razor'</span></span>
- <span data-ttu-id="5aa11-1325">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sí   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span><span class="sxs-lookup"><span data-stu-id="5aa11-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="5aa11-1327">Después de la parte `^rewrite-rule/` de la expresión, hay dos grupos de captura, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="5aa11-1328">`\d` significa *buscar un dígito (número)* .</span><span class="sxs-lookup"><span data-stu-id="5aa11-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="5aa11-1329">El signo más (`+`) significa *buscar uno o más de los caracteres anteriores*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="5aa11-1330">Por tanto, la URL debe contener un número seguido de una barra diagonal, seguida de otro número.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="5aa11-1331">Estos grupos de capturas se insertan en la URL de reescritura como `$1` y `$2`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="5aa11-1332">La cadena de reemplazo de la regla de reescritura coloca los grupos capturados en la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="5aa11-1333">La ruta de acceso solicitada de `/rewrite-rule/1234/5678` se reescribe para obtener el recurso en `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="5aa11-1334">Si una cadena de consulta está presente en la solicitud original, se conserva cuando se reescribe la dirección URL.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="5aa11-1335">No hay ningún recorrido de ida y vuelta al servidor para obtener el recurso.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="5aa11-1336">Si el recurso existe, se captura y se devuelve al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="5aa11-1337">Como el cliente no se redirige, la dirección URL no cambia en la barra de direcciones del explorador.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="5aa11-1338">Los clientes no pueden detectar que se ha producido una operación de reescritura de URL en el servidor.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="5aa11-1339">Use `skipRemainingRules: true` siempre que sea posible, ya que las reglas de coincidencia consumen muchos recursos y aumentan el tiempo de respuesta de aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="5aa11-1340">Para obtener la respuesta más rápida de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="5aa11-1341">Ordene las reglas de reescritura desde la que coincida con más frecuencia a la que coincida con menos frecuencia.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="5aa11-1342">Omita el procesamiento de las reglas restantes cuando se produzca una coincidencia; no es necesario ningún procesamiento de reglas adicional.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="5aa11-1343">mod_rewrite de Apache</span><span class="sxs-lookup"><span data-stu-id="5aa11-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="5aa11-1344">Aplique reglas mod_rewrite de Apache con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="5aa11-1345">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="5aa11-1346">Para obtener más información y ejemplos de reglas mod_rewrite, vea [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="5aa11-1347">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="5aa11-1348">La aplicación de ejemplo redirige las solicitudes de `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="5aa11-1349">El código de estado de la respuesta es *302: Encontrado*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="5aa11-1350">Solicitud original: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="5aa11-1352">El middleware admite las siguientes variables de servidor mod_rewrite de Apache:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="5aa11-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="5aa11-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="5aa11-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="5aa11-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="5aa11-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="5aa11-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="5aa11-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="5aa11-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="5aa11-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="5aa11-1358">HTTP_HOST</span></span>
* <span data-ttu-id="5aa11-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="5aa11-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="5aa11-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="5aa11-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="5aa11-1361">HTTPS</span></span>
* <span data-ttu-id="5aa11-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="5aa11-1362">IPV6</span></span>
* <span data-ttu-id="5aa11-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="5aa11-1363">QUERY_STRING</span></span>
* <span data-ttu-id="5aa11-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="5aa11-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="5aa11-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="5aa11-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="5aa11-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="5aa11-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="5aa11-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="5aa11-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="5aa11-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="5aa11-1369">REQUEST_URI</span></span>
* <span data-ttu-id="5aa11-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="5aa11-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="5aa11-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="5aa11-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1372">SERVER_PORT</span></span>
* <span data-ttu-id="5aa11-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="5aa11-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="5aa11-1374">TIME</span></span>
* <span data-ttu-id="5aa11-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="5aa11-1375">TIME_DAY</span></span>
* <span data-ttu-id="5aa11-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1376">TIME_HOUR</span></span>
* <span data-ttu-id="5aa11-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="5aa11-1377">TIME_MIN</span></span>
* <span data-ttu-id="5aa11-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="5aa11-1378">TIME_MON</span></span>
* <span data-ttu-id="5aa11-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="5aa11-1379">TIME_SEC</span></span>
* <span data-ttu-id="5aa11-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="5aa11-1380">TIME_WDAY</span></span>
* <span data-ttu-id="5aa11-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="5aa11-1382">Reglas del Módulo URL Rewrite para IIS</span><span class="sxs-lookup"><span data-stu-id="5aa11-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="5aa11-1383">Para usar el mismo conjunto de reglas que se aplica al módulo URL Rewrite para IIS, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="5aa11-1384">Asegúrese de que el archivo de reglas se implementa con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="5aa11-1385">No dirija el middleware para que use el archivo *web.config* de la aplicación cuando se ejecute en Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="5aa11-1386">Con IIS, estas reglas se deben almacenar fuera del archivo *web.config* de la aplicación para evitar conflictos con el Módulo URL Rewrite para IIS.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="5aa11-1387">Para obtener más información y ejemplos de reglas del Módulo URL Rewrite para IIS, vea [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0) y [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="5aa11-1388">Se usa <xref:System.IO.StreamReader> para leer las reglas del archivo de reglas *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="5aa11-1389">La aplicación de ejemplo reescribe las solicitudes de `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="5aa11-1390">La respuesta se envía al cliente con un código de estado *200 - Correcto*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="5aa11-1391">Solicitud original: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="5aa11-1393">Si tiene un Módulo URL Rewrite para IIS activo con reglas configuradas en el nivel de servidor que podrían afectar a la aplicación de manera no deseada, puede deshabilitar el Módulo URL Rewrite para IIS para una aplicación.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="5aa11-1394">Para más información, vea [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Deshabilitación de módulos de IIS).</span><span class="sxs-lookup"><span data-stu-id="5aa11-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="5aa11-1395">Características no admitidas</span><span class="sxs-lookup"><span data-stu-id="5aa11-1395">Unsupported features</span></span>

<span data-ttu-id="5aa11-1396">El middleware publicado con ASP.NET Core 2.x no admite las siguientes características de Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="5aa11-1397">Reglas de salida</span><span class="sxs-lookup"><span data-stu-id="5aa11-1397">Outbound Rules</span></span>
* <span data-ttu-id="5aa11-1398">Variables de servidor personalizadas</span><span class="sxs-lookup"><span data-stu-id="5aa11-1398">Custom Server Variables</span></span>
* <span data-ttu-id="5aa11-1399">Caracteres comodín</span><span class="sxs-lookup"><span data-stu-id="5aa11-1399">Wildcards</span></span>
* <span data-ttu-id="5aa11-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="5aa11-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="5aa11-1401">Variables de servidor compatibles</span><span class="sxs-lookup"><span data-stu-id="5aa11-1401">Supported server variables</span></span>

<span data-ttu-id="5aa11-1402">El middleware admite las siguientes variables de servidor del Módulo URL Rewrite para IIS:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="5aa11-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="5aa11-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="5aa11-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="5aa11-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="5aa11-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="5aa11-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="5aa11-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="5aa11-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="5aa11-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="5aa11-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="5aa11-1408">HTTP_HOST</span></span>
* <span data-ttu-id="5aa11-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="5aa11-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="5aa11-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1410">HTTP_URL</span></span>
* <span data-ttu-id="5aa11-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="5aa11-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="5aa11-1412">HTTPS</span></span>
* <span data-ttu-id="5aa11-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="5aa11-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="5aa11-1414">QUERY_STRING</span></span>
* <span data-ttu-id="5aa11-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="5aa11-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="5aa11-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="5aa11-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="5aa11-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="5aa11-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="5aa11-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="5aa11-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="5aa11-1419">También puede obtener <xref:Microsoft.Extensions.FileProviders.IFileProvider> a través de <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="5aa11-1420">Con este enfoque logrará mayor flexibilidad para la ubicación de los archivos de reglas de reescritura.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="5aa11-1421">Asegúrese de que los archivos de reglas de reescritura se implementan en el servidor en la ruta de acceso que proporcione.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="5aa11-1422">Regla basada en métodos</span><span class="sxs-lookup"><span data-stu-id="5aa11-1422">Method-based rule</span></span>

<span data-ttu-id="5aa11-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para implementar su propia lógica de la regla en un método.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="5aa11-1424">`Add` expone el elemento <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, lo que hace que <xref:Microsoft.AspNetCore.Http.HttpContext> esté disponible para usarlo en el método.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="5aa11-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina cómo se administra el procesamiento adicional en la canalización.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="5aa11-1426">Establezca el valor en uno de los campos <xref:Microsoft.AspNetCore.Rewrite.RuleResult> que se describen en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="5aa11-1427">Acción</span><span class="sxs-lookup"><span data-stu-id="5aa11-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="5aa11-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1429">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1429">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1430">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1430">'Identity'</span></span>
- <span data-ttu-id="5aa11-1431">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1432">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1432">'Razor'</span></span>
- <span data-ttu-id="5aa11-1433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1435">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1435">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1436">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1436">'Identity'</span></span>
- <span data-ttu-id="5aa11-1437">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1438">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1438">'Razor'</span></span>
- <span data-ttu-id="5aa11-1439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1441">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1441">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1442">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1442">'Identity'</span></span>
- <span data-ttu-id="5aa11-1443">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1444">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1444">'Razor'</span></span>
- <span data-ttu-id="5aa11-1445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1447">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1447">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1448">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1448">'Identity'</span></span>
- <span data-ttu-id="5aa11-1449">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1450">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1450">'Razor'</span></span>
- <span data-ttu-id="5aa11-1451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1453">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1453">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1454">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1454">'Identity'</span></span>
- <span data-ttu-id="5aa11-1455">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1456">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1456">'Razor'</span></span>
- <span data-ttu-id="5aa11-1457">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1459">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1459">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1460">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1460">'Identity'</span></span>
- <span data-ttu-id="5aa11-1461">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1462">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1462">'Razor'</span></span>
- <span data-ttu-id="5aa11-1463">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1465">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1465">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1466">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1466">'Identity'</span></span>
- <span data-ttu-id="5aa11-1467">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1468">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1468">'Razor'</span></span>
- <span data-ttu-id="5aa11-1469">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1471">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1471">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1472">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1472">'Identity'</span></span>
- <span data-ttu-id="5aa11-1473">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1474">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1474">'Razor'</span></span>
- <span data-ttu-id="5aa11-1475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1477">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1477">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1478">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1478">'Identity'</span></span>
- <span data-ttu-id="5aa11-1479">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1480">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1480">'Razor'</span></span>
- <span data-ttu-id="5aa11-1481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1483">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1483">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1484">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1484">'Identity'</span></span>
- <span data-ttu-id="5aa11-1485">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1486">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1486">'Razor'</span></span>
- <span data-ttu-id="5aa11-1487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1489">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1489">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1490">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1490">'Identity'</span></span>
- <span data-ttu-id="5aa11-1491">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1492">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1492">'Razor'</span></span>
- <span data-ttu-id="5aa11-1493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1495">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1495">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1496">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1496">'Identity'</span></span>
- <span data-ttu-id="5aa11-1497">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1498">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1498">'Razor'</span></span>
- <span data-ttu-id="5aa11-1499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1501">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1501">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1502">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1502">'Identity'</span></span>
- <span data-ttu-id="5aa11-1503">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1504">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1504">'Razor'</span></span>
- <span data-ttu-id="5aa11-1505">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1507">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1507">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1508">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1508">'Identity'</span></span>
- <span data-ttu-id="5aa11-1509">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1510">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1510">'Razor'</span></span>
- <span data-ttu-id="5aa11-1511">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1513">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1513">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1514">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1514">'Identity'</span></span>
- <span data-ttu-id="5aa11-1515">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1516">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1516">'Razor'</span></span>
- <span data-ttu-id="5aa11-1517">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1519">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1519">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1520">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1520">'Identity'</span></span>
- <span data-ttu-id="5aa11-1521">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1522">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1522">'Razor'</span></span>
- <span data-ttu-id="5aa11-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1525">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1525">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1526">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1526">'Identity'</span></span>
- <span data-ttu-id="5aa11-1527">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1528">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1528">'Razor'</span></span>
- <span data-ttu-id="5aa11-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1531">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1531">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1532">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1532">'Identity'</span></span>
- <span data-ttu-id="5aa11-1533">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1534">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1534">'Razor'</span></span>
- <span data-ttu-id="5aa11-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1537">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1537">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1538">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1538">'Identity'</span></span>
- <span data-ttu-id="5aa11-1539">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1540">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1540">'Razor'</span></span>
- <span data-ttu-id="5aa11-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1543">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1543">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1544">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1544">'Identity'</span></span>
- <span data-ttu-id="5aa11-1545">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1546">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1546">'Razor'</span></span>
- <span data-ttu-id="5aa11-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1549">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1549">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1550">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1550">'Identity'</span></span>
- <span data-ttu-id="5aa11-1551">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1552">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1552">'Razor'</span></span>
- <span data-ttu-id="5aa11-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1555">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1555">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1556">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1556">'Identity'</span></span>
- <span data-ttu-id="5aa11-1557">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1558">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1558">'Razor'</span></span>
- <span data-ttu-id="5aa11-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1561">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1561">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1562">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1562">'Identity'</span></span>
- <span data-ttu-id="5aa11-1563">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1564">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1564">'Razor'</span></span>
- <span data-ttu-id="5aa11-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1567">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1567">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1568">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1568">'Identity'</span></span>
- <span data-ttu-id="5aa11-1569">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1570">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1570">'Razor'</span></span>
- <span data-ttu-id="5aa11-1571">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1573">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1573">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1574">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1574">'Identity'</span></span>
- <span data-ttu-id="5aa11-1575">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1576">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1576">'Razor'</span></span>
- <span data-ttu-id="5aa11-1577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1579">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1579">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1580">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1580">'Identity'</span></span>
- <span data-ttu-id="5aa11-1581">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1582">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1582">'Razor'</span></span>
- <span data-ttu-id="5aa11-1583">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1585">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1585">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1586">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1586">'Identity'</span></span>
- <span data-ttu-id="5aa11-1587">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1588">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1588">'Razor'</span></span>
- <span data-ttu-id="5aa11-1589">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1591">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1591">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1592">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1592">'Identity'</span></span>
- <span data-ttu-id="5aa11-1593">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1594">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1594">'Razor'</span></span>
- <span data-ttu-id="5aa11-1595">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1597">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1597">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1598">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1598">'Identity'</span></span>
- <span data-ttu-id="5aa11-1599">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1600">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1600">'Razor'</span></span>
- <span data-ttu-id="5aa11-1601">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1603">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1603">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1604">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1604">'Identity'</span></span>
- <span data-ttu-id="5aa11-1605">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1606">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1606">'Razor'</span></span>
- <span data-ttu-id="5aa11-1607">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1609">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1609">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1610">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1610">'Identity'</span></span>
- <span data-ttu-id="5aa11-1611">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1612">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1612">'Razor'</span></span>
- <span data-ttu-id="5aa11-1613">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1615">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1615">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1616">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1616">'Identity'</span></span>
- <span data-ttu-id="5aa11-1617">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1618">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1618">'Razor'</span></span>
- <span data-ttu-id="5aa11-1619">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1621">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1621">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1622">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1622">'Identity'</span></span>
- <span data-ttu-id="5aa11-1623">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1624">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1624">'Razor'</span></span>
- <span data-ttu-id="5aa11-1625">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1627">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1627">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1628">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1628">'Identity'</span></span>
- <span data-ttu-id="5aa11-1629">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1630">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1630">'Razor'</span></span>
- <span data-ttu-id="5aa11-1631">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1633">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1633">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1634">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1634">'Identity'</span></span>
- <span data-ttu-id="5aa11-1635">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1636">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1636">'Razor'</span></span>
- <span data-ttu-id="5aa11-1637">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1639">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1639">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1640">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1640">'Identity'</span></span>
- <span data-ttu-id="5aa11-1641">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1642">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1642">'Razor'</span></span>
- <span data-ttu-id="5aa11-1643">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1645">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1645">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1646">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1646">'Identity'</span></span>
- <span data-ttu-id="5aa11-1647">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1648">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1648">'Razor'</span></span>
- <span data-ttu-id="5aa11-1649">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1651">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1651">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1652">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1652">'Identity'</span></span>
- <span data-ttu-id="5aa11-1653">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1654">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1654">'Razor'</span></span>
- <span data-ttu-id="5aa11-1655">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1657">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1657">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1658">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1658">'Identity'</span></span>
- <span data-ttu-id="5aa11-1659">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1660">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1660">'Razor'</span></span>
- <span data-ttu-id="5aa11-1661">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1663">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1663">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1664">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1664">'Identity'</span></span>
- <span data-ttu-id="5aa11-1665">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1666">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1666">'Razor'</span></span>
- <span data-ttu-id="5aa11-1667">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1669">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1669">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1670">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1670">'Identity'</span></span>
- <span data-ttu-id="5aa11-1671">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1672">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1672">'Razor'</span></span>
- <span data-ttu-id="5aa11-1673">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1675">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1675">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1676">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1676">'Identity'</span></span>
- <span data-ttu-id="5aa11-1677">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1678">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1678">'Razor'</span></span>
- <span data-ttu-id="5aa11-1679">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1681">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1681">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1682">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1682">'Identity'</span></span>
- <span data-ttu-id="5aa11-1683">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1684">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1684">'Razor'</span></span>
- <span data-ttu-id="5aa11-1685">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1687">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1687">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1688">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1688">'Identity'</span></span>
- <span data-ttu-id="5aa11-1689">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1690">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1690">'Razor'</span></span>
- <span data-ttu-id="5aa11-1691">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1693">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1693">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1694">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1694">'Identity'</span></span>
- <span data-ttu-id="5aa11-1695">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1696">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1696">'Razor'</span></span>
- <span data-ttu-id="5aa11-1697">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1699">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1699">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1700">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1700">'Identity'</span></span>
- <span data-ttu-id="5aa11-1701">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1702">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1702">'Razor'</span></span>
- <span data-ttu-id="5aa11-1703">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1704">-------------------------------- | | `RuleResult.ContinueRules` (valor predeterminado) | Continuar aplicando las reglas.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="5aa11-1705">| | `RuleResult.EndResponse`             | Dejar de aplicar las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="5aa11-1706">| | `RuleResult.SkipRemainingRules`      | Dejar de aplicar las reglas y enviar el contexto al middleware siguiente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="5aa11-1707">La aplicación de ejemplo muestra un método que redirige las solicitudes para las rutas de acceso que terminen con *.xml*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="5aa11-1708">Si se realiza una solicitud de `/file.xml`, la solicitud se redirige a `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="5aa11-1709">El código de estado se establece en *301 - Movido definitivamente*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="5aa11-1710">Cuando el explorador realiza una solicitud nueva a */xmlfiles/file.xml*, el middleware de archivos estáticos sirve el archivo al cliente desde la carpeta *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="5aa11-1711">Para un redireccionamiento, debe establecer de forma explícita el código de estado de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="5aa11-1712">En caso contrario, se devuelve un código de estado *200: correcto* y no se produce el redireccionamiento en el cliente.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="5aa11-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="5aa11-1714">Este enfoque también permite volver a escribir las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="5aa11-1715">En la aplicación de ejemplo se muestra cómo volver a escribir la ruta de acceso para cualquier solicitud de archivo de texto para proporcionar el archivo de texto *file.txt* desde la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="5aa11-1716">El middleware de archivos estáticos proporciona el archivo en función de la ruta de acceso de la solicitud actualizada:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="5aa11-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="5aa11-1718">Regla basada en IRule</span><span class="sxs-lookup"><span data-stu-id="5aa11-1718">IRule-based rule</span></span>

<span data-ttu-id="5aa11-1719">Utilice <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> para usar la lógica de reglas en una clase que implemente la interfaz <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="5aa11-1720">`IRule` proporciona mayor flexibilidad que si se usa el enfoque de reglas basadas en métodos.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="5aa11-1721">La clase de implementación puede incluir un constructor que permita pasar parámetros para el método <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="5aa11-1722">Se comprueba que los valores de los parámetros en la aplicación de ejemplo para `extension` y `newPath` cumplen ciertas condiciones.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="5aa11-1723">`extension` debe contener un valor, que debe ser *.png*, *.jpg* o *.gif*.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="5aa11-1724">Si `newPath` no es válido, se genera <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="5aa11-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="5aa11-1725">Si se realiza una solicitud de *image.png*, la solicitud se redirige a `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="5aa11-1726">Si se realiza una solicitud de *image.jpg*, la solicitud se redirige a `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="5aa11-1727">El código de estado se establece en *301 - Movido definitivamente* y se establece `context.Result` para detener el procesamiento de las reglas y enviar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="5aa11-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="5aa11-1728">Solicitud original: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1728">Original Request: `/image.png`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="5aa11-1730">Solicitud original: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1730">Original Request: `/image.jpg`</span></span>

![Ventana del explorador con herramientas de desarrollo realizando un seguimiento de las solicitudes y las respuestas para image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="5aa11-1732">Ejemplos de expresiones regulares</span><span class="sxs-lookup"><span data-stu-id="5aa11-1732">Regex examples</span></span>

| <span data-ttu-id="5aa11-1733">Objetivo</span><span class="sxs-lookup"><span data-stu-id="5aa11-1733">Goal</span></span> | <span data-ttu-id="5aa11-1734">Cadena de expresión regular &</span><span class="sxs-lookup"><span data-stu-id="5aa11-1734">Regex String &</span></span><br><span data-ttu-id="5aa11-1735">Ejemplo de coincidencia</span><span class="sxs-lookup"><span data-stu-id="5aa11-1735">Match Example</span></span> | <span data-ttu-id="5aa11-1736">Cadena de reemplazo &</span><span class="sxs-lookup"><span data-stu-id="5aa11-1736">Replacement String &</span></span><br><span data-ttu-id="5aa11-1737">Ejemplo de resultado</span><span class="sxs-lookup"><span data-stu-id="5aa11-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="5aa11-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1739">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1739">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1740">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1740">'Identity'</span></span>
- <span data-ttu-id="5aa11-1741">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1742">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1742">'Razor'</span></span>
- <span data-ttu-id="5aa11-1743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1745">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1745">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1746">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1746">'Identity'</span></span>
- <span data-ttu-id="5aa11-1747">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1748">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1748">'Razor'</span></span>
- <span data-ttu-id="5aa11-1749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1751">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1751">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1752">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1752">'Identity'</span></span>
- <span data-ttu-id="5aa11-1753">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1754">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1754">'Razor'</span></span>
- <span data-ttu-id="5aa11-1755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1757">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1757">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1758">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1758">'Identity'</span></span>
- <span data-ttu-id="5aa11-1759">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1760">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1760">'Razor'</span></span>
- <span data-ttu-id="5aa11-1761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1763">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1763">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1764">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1764">'Identity'</span></span>
- <span data-ttu-id="5aa11-1765">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1766">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1766">'Razor'</span></span>
- <span data-ttu-id="5aa11-1767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1769">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1769">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1770">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1770">'Identity'</span></span>
- <span data-ttu-id="5aa11-1771">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1772">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1772">'Razor'</span></span>
- <span data-ttu-id="5aa11-1773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1775">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1775">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1776">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1776">'Identity'</span></span>
- <span data-ttu-id="5aa11-1777">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1778">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1778">'Razor'</span></span>
- <span data-ttu-id="5aa11-1779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1781">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1781">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1782">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1782">'Identity'</span></span>
- <span data-ttu-id="5aa11-1783">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1784">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1784">'Razor'</span></span>
- <span data-ttu-id="5aa11-1785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1787">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1787">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1788">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1788">'Identity'</span></span>
- <span data-ttu-id="5aa11-1789">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1790">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1790">'Razor'</span></span>
- <span data-ttu-id="5aa11-1791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1793">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1793">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1794">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1794">'Identity'</span></span>
- <span data-ttu-id="5aa11-1795">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1796">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1796">'Razor'</span></span>
- <span data-ttu-id="5aa11-1797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1799">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1799">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1800">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1800">'Identity'</span></span>
- <span data-ttu-id="5aa11-1801">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1802">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1802">'Razor'</span></span>
- <span data-ttu-id="5aa11-1803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1805">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1805">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1806">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1806">'Identity'</span></span>
- <span data-ttu-id="5aa11-1807">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1808">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1808">'Razor'</span></span>
- <span data-ttu-id="5aa11-1809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1811">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1811">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1812">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1812">'Identity'</span></span>
- <span data-ttu-id="5aa11-1813">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1814">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1814">'Razor'</span></span>
- <span data-ttu-id="5aa11-1815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1817">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1817">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1818">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1818">'Identity'</span></span>
- <span data-ttu-id="5aa11-1819">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1820">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1820">'Razor'</span></span>
- <span data-ttu-id="5aa11-1821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1823">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1823">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1824">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1824">'Identity'</span></span>
- <span data-ttu-id="5aa11-1825">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1826">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1826">'Razor'</span></span>
- <span data-ttu-id="5aa11-1827">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1829">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1829">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1830">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1830">'Identity'</span></span>
- <span data-ttu-id="5aa11-1831">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1832">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1832">'Razor'</span></span>
- <span data-ttu-id="5aa11-1833">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1835">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1835">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1836">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1836">'Identity'</span></span>
- <span data-ttu-id="5aa11-1837">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1838">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1838">'Razor'</span></span>
- <span data-ttu-id="5aa11-1839">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1841">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1841">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1842">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1842">'Identity'</span></span>
- <span data-ttu-id="5aa11-1843">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1844">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1844">'Razor'</span></span>
- <span data-ttu-id="5aa11-1845">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1847">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1847">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1848">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1848">'Identity'</span></span>
- <span data-ttu-id="5aa11-1849">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1850">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1850">'Razor'</span></span>
- <span data-ttu-id="5aa11-1851">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1853">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1853">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1854">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1854">'Identity'</span></span>
- <span data-ttu-id="5aa11-1855">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1856">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1856">'Razor'</span></span>
- <span data-ttu-id="5aa11-1857">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1859">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1859">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1860">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1860">'Identity'</span></span>
- <span data-ttu-id="5aa11-1861">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1862">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1862">'Razor'</span></span>
- <span data-ttu-id="5aa11-1863">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1865">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1865">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1866">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1866">'Identity'</span></span>
- <span data-ttu-id="5aa11-1867">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1868">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1868">'Razor'</span></span>
- <span data-ttu-id="5aa11-1869">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1871">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1871">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1872">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1872">'Identity'</span></span>
- <span data-ttu-id="5aa11-1873">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1874">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1874">'Razor'</span></span>
- <span data-ttu-id="5aa11-1875">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1877">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1877">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1878">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1878">'Identity'</span></span>
- <span data-ttu-id="5aa11-1879">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1880">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1880">'Razor'</span></span>
- <span data-ttu-id="5aa11-1881">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1883">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1883">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1884">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1884">'Identity'</span></span>
- <span data-ttu-id="5aa11-1885">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1886">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1886">'Razor'</span></span>
- <span data-ttu-id="5aa11-1887">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1889">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1889">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1890">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1890">'Identity'</span></span>
- <span data-ttu-id="5aa11-1891">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1892">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1892">'Razor'</span></span>
- <span data-ttu-id="5aa11-1893">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1895">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1895">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1896">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1896">'Identity'</span></span>
- <span data-ttu-id="5aa11-1897">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1898">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1898">'Razor'</span></span>
- <span data-ttu-id="5aa11-1899">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1901">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1901">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1902">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1902">'Identity'</span></span>
- <span data-ttu-id="5aa11-1903">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1904">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1904">'Razor'</span></span>
- <span data-ttu-id="5aa11-1905">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1907">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1907">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1908">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1908">'Identity'</span></span>
- <span data-ttu-id="5aa11-1909">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1910">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1910">'Razor'</span></span>
- <span data-ttu-id="5aa11-1911">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5aa11-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5aa11-1913">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1913">'Blazor'</span></span>
- <span data-ttu-id="5aa11-1914">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1914">'Identity'</span></span>
- <span data-ttu-id="5aa11-1915">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="5aa11-1916">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5aa11-1916">'Razor'</span></span>
- <span data-ttu-id="5aa11-1917">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5aa11-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="5aa11-1918">------------------- | | Ruta de acceso de reescritura en la cadena de consulta | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="5aa11-1919">`/path?var1=abc&var2=123` | | Quitar barra diagonal final | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="5aa11-1920">`/path` | | Exigir barra diagonal final | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="5aa11-1921">`/path/` | | Evitar reescritura de solicitudes específicas | `^(.*)(?<!\.axd)$` o `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="5aa11-1922">Sí: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="5aa11-1923">No: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="5aa11-1924">`/resource.axd` | | Reorganizar los segmentos de URL | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="5aa11-1925">`path/3/2/1` | | Reemplazar un segmento de URL | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="5aa11-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5aa11-1926">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5aa11-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="5aa11-1927">Expresiones regulares en .NET</span><span class="sxs-lookup"><span data-stu-id="5aa11-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="5aa11-1928">Lenguaje de expresiones regulares: referencia rápida</span><span class="sxs-lookup"><span data-stu-id="5aa11-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* <span data-ttu-id="5aa11-1929">[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/) (mod_rewrite de Apache)</span><span class="sxs-lookup"><span data-stu-id="5aa11-1929">[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* <span data-ttu-id="5aa11-1930">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso del Módulo URL Rewrite 2.0 para IIS)</span><span class="sxs-lookup"><span data-stu-id="5aa11-1930">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)</span></span>
* <span data-ttu-id="5aa11-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Referencia de configuración del Módulo URL Rewrite)</span><span class="sxs-lookup"><span data-stu-id="5aa11-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* <span data-ttu-id="5aa11-1932">[IIS URL Rewrite Module Forum](https://forums.iis.net/1152.aspx) (Foro del Módulo URL Rewrite para IIS)</span><span class="sxs-lookup"><span data-stu-id="5aa11-1932">[IIS URL Rewrite Module Forum](https://forums.iis.net/1152.aspx)</span></span>
* [<span data-ttu-id="5aa11-1933">Cómo simplificar la estructura de direcciones URL</span><span class="sxs-lookup"><span data-stu-id="5aa11-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* <span data-ttu-id="5aa11-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/) (10 trucos y consejos para reescritura de URL)</span><span class="sxs-lookup"><span data-stu-id="5aa11-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="5aa11-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html) (Usar la barra diagonal o no)</span><span class="sxs-lookup"><span data-stu-id="5aa11-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
