---
<span data-ttu-id="4c36d-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-102">'Blazor'</span></span>
- <span data-ttu-id="4c36d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-103">'Identity'</span></span>
- <span data-ttu-id="4c36d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-105">'Razor'</span></span>
- <span data-ttu-id="4c36d-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="4c36d-107">Almacenamiento en caché de respuestas en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c36d-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="4c36d-108">Por [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4c36d-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4c36d-109">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c36d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4c36d-110">El almacenamiento en caché de respuestas reduce el número de solicitudes que un cliente o proxy realiza a un servidor Web.</span><span class="sxs-lookup"><span data-stu-id="4c36d-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="4c36d-111">El almacenamiento en caché de respuestas también reduce la cantidad de trabajo que realiza el servidor web para generar una respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="4c36d-112">El almacenamiento en caché de respuestas está controlado por encabezados que especifican cómo desea que el cliente, el proxy y el middleware almacenen en caché las respuestas.</span><span class="sxs-lookup"><span data-stu-id="4c36d-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="4c36d-113">El [atributo ResponseCache](#responsecache-attribute) participa en el establecimiento de encabezados de caché de respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="4c36d-114">Los clientes y los servidores proxy intermedios deben respetar los encabezados para almacenar en caché las respuestas en la [especificación de almacenamiento en caché HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="4c36d-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="4c36d-115">Para el almacenamiento en caché del lado servidor que sigue la especificación HTTP 1,1 Caching, utilice el [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="4c36d-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="4c36d-116">El middleware puede usar las <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> propiedades para influir en el comportamiento del almacenamiento en caché del servidor.</span><span class="sxs-lookup"><span data-stu-id="4c36d-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="4c36d-117">Almacenamiento en caché de respuesta basada en HTTP</span><span class="sxs-lookup"><span data-stu-id="4c36d-117">HTTP-based response caching</span></span>

<span data-ttu-id="4c36d-118">La [especificación HTTP 1,1 Caching](https://tools.ietf.org/html/rfc7234) describe el comportamiento de las memorias caché de Internet.</span><span class="sxs-lookup"><span data-stu-id="4c36d-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="4c36d-119">El encabezado HTTP principal usado para el almacenamiento en caché es [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), que se usa para especificar *directivas*de caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="4c36d-120">Las directivas controlan el comportamiento de almacenamiento en caché a medida que las solicitudes proceden de los clientes a los servidores y, a medida que las respuestas, vuelven desde los servidores a los clientes.</span><span class="sxs-lookup"><span data-stu-id="4c36d-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="4c36d-121">Las solicitudes y respuestas se desplazan a través de servidores proxy y los servidores proxy también deben cumplir con la especificación de almacenamiento en caché HTTP 1,1.</span><span class="sxs-lookup"><span data-stu-id="4c36d-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="4c36d-122">`Cache-Control`En la tabla siguiente se muestran las directivas comunes.</span><span class="sxs-lookup"><span data-stu-id="4c36d-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="4c36d-123">Directiva</span><span class="sxs-lookup"><span data-stu-id="4c36d-123">Directive</span></span>                                                       | <span data-ttu-id="4c36d-124">Acción</span><span class="sxs-lookup"><span data-stu-id="4c36d-124">Action</span></span> |
| ---
<span data-ttu-id="4c36d-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-126">'Blazor'</span></span>
- <span data-ttu-id="4c36d-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-127">'Identity'</span></span>
- <span data-ttu-id="4c36d-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-129">'Razor'</span></span>
- <span data-ttu-id="4c36d-130">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-132">'Blazor'</span></span>
- <span data-ttu-id="4c36d-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-133">'Identity'</span></span>
- <span data-ttu-id="4c36d-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-135">'Razor'</span></span>
- <span data-ttu-id="4c36d-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-138">'Blazor'</span></span>
- <span data-ttu-id="4c36d-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-139">'Identity'</span></span>
- <span data-ttu-id="4c36d-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-141">'Razor'</span></span>
- <span data-ttu-id="4c36d-142">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-144">'Blazor'</span></span>
- <span data-ttu-id="4c36d-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-145">'Identity'</span></span>
- <span data-ttu-id="4c36d-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-147">'Razor'</span></span>
- <span data-ttu-id="4c36d-148">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-150">'Blazor'</span></span>
- <span data-ttu-id="4c36d-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-151">'Identity'</span></span>
- <span data-ttu-id="4c36d-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-153">'Razor'</span></span>
- <span data-ttu-id="4c36d-154">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-156">'Blazor'</span></span>
- <span data-ttu-id="4c36d-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-157">'Identity'</span></span>
- <span data-ttu-id="4c36d-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-159">'Razor'</span></span>
- <span data-ttu-id="4c36d-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-162">'Blazor'</span></span>
- <span data-ttu-id="4c36d-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-163">'Identity'</span></span>
- <span data-ttu-id="4c36d-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-165">'Razor'</span></span>
- <span data-ttu-id="4c36d-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-168">'Blazor'</span></span>
- <span data-ttu-id="4c36d-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-169">'Identity'</span></span>
- <span data-ttu-id="4c36d-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-171">'Razor'</span></span>
- <span data-ttu-id="4c36d-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-174">'Blazor'</span></span>
- <span data-ttu-id="4c36d-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-175">'Identity'</span></span>
- <span data-ttu-id="4c36d-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-177">'Razor'</span></span>
- <span data-ttu-id="4c36d-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-180">'Blazor'</span></span>
- <span data-ttu-id="4c36d-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-181">'Identity'</span></span>
- <span data-ttu-id="4c36d-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-183">'Razor'</span></span>
- <span data-ttu-id="4c36d-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-186">'Blazor'</span></span>
- <span data-ttu-id="4c36d-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-187">'Identity'</span></span>
- <span data-ttu-id="4c36d-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-189">'Razor'</span></span>
- <span data-ttu-id="4c36d-190">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-192">'Blazor'</span></span>
- <span data-ttu-id="4c36d-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-193">'Identity'</span></span>
- <span data-ttu-id="4c36d-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-195">'Razor'</span></span>
- <span data-ttu-id="4c36d-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-198">'Blazor'</span></span>
- <span data-ttu-id="4c36d-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-199">'Identity'</span></span>
- <span data-ttu-id="4c36d-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-201">'Razor'</span></span>
- <span data-ttu-id="4c36d-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-204">'Blazor'</span></span>
- <span data-ttu-id="4c36d-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-205">'Identity'</span></span>
- <span data-ttu-id="4c36d-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-207">'Razor'</span></span>
- <span data-ttu-id="4c36d-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-210">'Blazor'</span></span>
- <span data-ttu-id="4c36d-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-211">'Identity'</span></span>
- <span data-ttu-id="4c36d-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-213">'Razor'</span></span>
- <span data-ttu-id="4c36d-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-216">'Blazor'</span></span>
- <span data-ttu-id="4c36d-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-217">'Identity'</span></span>
- <span data-ttu-id="4c36d-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-219">'Razor'</span></span>
- <span data-ttu-id="4c36d-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-222">'Blazor'</span></span>
- <span data-ttu-id="4c36d-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-223">'Identity'</span></span>
- <span data-ttu-id="4c36d-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-225">'Razor'</span></span>
- <span data-ttu-id="4c36d-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-228">'Blazor'</span></span>
- <span data-ttu-id="4c36d-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-229">'Identity'</span></span>
- <span data-ttu-id="4c36d-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-231">'Razor'</span></span>
- <span data-ttu-id="4c36d-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-234">'Blazor'</span></span>
- <span data-ttu-id="4c36d-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-235">'Identity'</span></span>
- <span data-ttu-id="4c36d-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-237">'Razor'</span></span>
- <span data-ttu-id="4c36d-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-240">'Blazor'</span></span>
- <span data-ttu-id="4c36d-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-241">'Identity'</span></span>
- <span data-ttu-id="4c36d-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-243">'Razor'</span></span>
- <span data-ttu-id="4c36d-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-246">'Blazor'</span></span>
- <span data-ttu-id="4c36d-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-247">'Identity'</span></span>
- <span data-ttu-id="4c36d-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-249">'Razor'</span></span>
- <span data-ttu-id="4c36d-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-252">'Blazor'</span></span>
- <span data-ttu-id="4c36d-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-253">'Identity'</span></span>
- <span data-ttu-id="4c36d-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-255">'Razor'</span></span>
- <span data-ttu-id="4c36d-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-258">'Blazor'</span></span>
- <span data-ttu-id="4c36d-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-259">'Identity'</span></span>
- <span data-ttu-id="4c36d-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-261">'Razor'</span></span>
- <span data-ttu-id="4c36d-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-264">'Blazor'</span></span>
- <span data-ttu-id="4c36d-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-265">'Identity'</span></span>
- <span data-ttu-id="4c36d-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-267">'Razor'</span></span>
- <span data-ttu-id="4c36d-268">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-270">'Blazor'</span></span>
- <span data-ttu-id="4c36d-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-271">'Identity'</span></span>
- <span data-ttu-id="4c36d-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-273">'Razor'</span></span>
- <span data-ttu-id="4c36d-274">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-276">'Blazor'</span></span>
- <span data-ttu-id="4c36d-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-277">'Identity'</span></span>
- <span data-ttu-id="4c36d-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-279">'Razor'</span></span>
- <span data-ttu-id="4c36d-280">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-282">'Blazor'</span></span>
- <span data-ttu-id="4c36d-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-283">'Identity'</span></span>
- <span data-ttu-id="4c36d-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-285">'Razor'</span></span>
- <span data-ttu-id="4c36d-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-288">'Blazor'</span></span>
- <span data-ttu-id="4c36d-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-289">'Identity'</span></span>
- <span data-ttu-id="4c36d-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-291">'Razor'</span></span>
- <span data-ttu-id="4c36d-292">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-294">'Blazor'</span></span>
- <span data-ttu-id="4c36d-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-295">'Identity'</span></span>
- <span data-ttu-id="4c36d-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-297">'Razor'</span></span>
- <span data-ttu-id="4c36d-298">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-298">'SignalR' uid:</span></span> 

<span data-ttu-id="4c36d-299">-------------------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="4c36d-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-300">'Blazor'</span></span>
- <span data-ttu-id="4c36d-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-301">'Identity'</span></span>
- <span data-ttu-id="4c36d-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-303">'Razor'</span></span>
- <span data-ttu-id="4c36d-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-304">'SignalR' uid:</span></span> 

<span data-ttu-id="4c36d-305">--- | | [público](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Una memoria caché puede almacenar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="4c36d-306">| | [privado](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | La respuesta no debe almacenarse en una memoria caché compartida.</span><span class="sxs-lookup"><span data-stu-id="4c36d-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="4c36d-307">Una caché privada puede almacenar y reutilizar la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="4c36d-308">| | [Max-Age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | El cliente no acepta una respuesta cuya antigüedad sea mayor que el número de segundos especificado.</span><span class="sxs-lookup"><span data-stu-id="4c36d-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="4c36d-309">Ejemplos: `max-age=60` (60 segundos), `max-age=2592000` (1 mes) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **En las solicitudes**: una memoria caché no debe usar una respuesta almacenada para satisfacer la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4c36d-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="4c36d-310">El servidor de origen regenera la respuesta para el cliente y el middleware actualiza la respuesta almacenada en su caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="4c36d-311">**En las respuestas**: la respuesta no se debe utilizar para una solicitud subsiguiente sin validación en el servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="4c36d-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="4c36d-312">| | [sin almacén](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **En las solicitudes**: una memoria caché no debe almacenar la solicitud.</span><span class="sxs-lookup"><span data-stu-id="4c36d-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="4c36d-313">**En las respuestas**: una memoria caché no debe almacenar ninguna parte de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="4c36d-314">En la tabla siguiente se muestran otros encabezados de caché que desempeñan un rol en el almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="4c36d-315">Header</span><span class="sxs-lookup"><span data-stu-id="4c36d-315">Header</span></span>                                                     | <span data-ttu-id="4c36d-316">Función</span><span class="sxs-lookup"><span data-stu-id="4c36d-316">Function</span></span> |
| ---
<span data-ttu-id="4c36d-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-318">'Blazor'</span></span>
- <span data-ttu-id="4c36d-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-319">'Identity'</span></span>
- <span data-ttu-id="4c36d-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-321">'Razor'</span></span>
- <span data-ttu-id="4c36d-322">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-324">'Blazor'</span></span>
- <span data-ttu-id="4c36d-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-325">'Identity'</span></span>
- <span data-ttu-id="4c36d-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-327">'Razor'</span></span>
- <span data-ttu-id="4c36d-328">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-330">'Blazor'</span></span>
- <span data-ttu-id="4c36d-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-331">'Identity'</span></span>
- <span data-ttu-id="4c36d-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-333">'Razor'</span></span>
- <span data-ttu-id="4c36d-334">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-336">'Blazor'</span></span>
- <span data-ttu-id="4c36d-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-337">'Identity'</span></span>
- <span data-ttu-id="4c36d-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-339">'Razor'</span></span>
- <span data-ttu-id="4c36d-340">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-342">'Blazor'</span></span>
- <span data-ttu-id="4c36d-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-343">'Identity'</span></span>
- <span data-ttu-id="4c36d-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-345">'Razor'</span></span>
- <span data-ttu-id="4c36d-346">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-348">'Blazor'</span></span>
- <span data-ttu-id="4c36d-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-349">'Identity'</span></span>
- <span data-ttu-id="4c36d-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-351">'Razor'</span></span>
- <span data-ttu-id="4c36d-352">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-354">'Blazor'</span></span>
- <span data-ttu-id="4c36d-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-355">'Identity'</span></span>
- <span data-ttu-id="4c36d-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-357">'Razor'</span></span>
- <span data-ttu-id="4c36d-358">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-360">'Blazor'</span></span>
- <span data-ttu-id="4c36d-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-361">'Identity'</span></span>
- <span data-ttu-id="4c36d-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-363">'Razor'</span></span>
- <span data-ttu-id="4c36d-364">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-366">'Blazor'</span></span>
- <span data-ttu-id="4c36d-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-367">'Identity'</span></span>
- <span data-ttu-id="4c36d-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-369">'Razor'</span></span>
- <span data-ttu-id="4c36d-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-372">'Blazor'</span></span>
- <span data-ttu-id="4c36d-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-373">'Identity'</span></span>
- <span data-ttu-id="4c36d-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-375">'Razor'</span></span>
- <span data-ttu-id="4c36d-376">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-378">'Blazor'</span></span>
- <span data-ttu-id="4c36d-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-379">'Identity'</span></span>
- <span data-ttu-id="4c36d-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-381">'Razor'</span></span>
- <span data-ttu-id="4c36d-382">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-384">'Blazor'</span></span>
- <span data-ttu-id="4c36d-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-385">'Identity'</span></span>
- <span data-ttu-id="4c36d-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-387">'Razor'</span></span>
- <span data-ttu-id="4c36d-388">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-390">'Blazor'</span></span>
- <span data-ttu-id="4c36d-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-391">'Identity'</span></span>
- <span data-ttu-id="4c36d-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-393">'Razor'</span></span>
- <span data-ttu-id="4c36d-394">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-396">'Blazor'</span></span>
- <span data-ttu-id="4c36d-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-397">'Identity'</span></span>
- <span data-ttu-id="4c36d-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-399">'Razor'</span></span>
- <span data-ttu-id="4c36d-400">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-402">'Blazor'</span></span>
- <span data-ttu-id="4c36d-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-403">'Identity'</span></span>
- <span data-ttu-id="4c36d-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-405">'Razor'</span></span>
- <span data-ttu-id="4c36d-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-408">'Blazor'</span></span>
- <span data-ttu-id="4c36d-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-409">'Identity'</span></span>
- <span data-ttu-id="4c36d-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-411">'Razor'</span></span>
- <span data-ttu-id="4c36d-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-414">'Blazor'</span></span>
- <span data-ttu-id="4c36d-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-415">'Identity'</span></span>
- <span data-ttu-id="4c36d-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-417">'Razor'</span></span>
- <span data-ttu-id="4c36d-418">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-420">'Blazor'</span></span>
- <span data-ttu-id="4c36d-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-421">'Identity'</span></span>
- <span data-ttu-id="4c36d-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-423">'Razor'</span></span>
- <span data-ttu-id="4c36d-424">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-426">'Blazor'</span></span>
- <span data-ttu-id="4c36d-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-427">'Identity'</span></span>
- <span data-ttu-id="4c36d-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-429">'Razor'</span></span>
- <span data-ttu-id="4c36d-430">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-432">'Blazor'</span></span>
- <span data-ttu-id="4c36d-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-433">'Identity'</span></span>
- <span data-ttu-id="4c36d-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-435">'Razor'</span></span>
- <span data-ttu-id="4c36d-436">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-438">'Blazor'</span></span>
- <span data-ttu-id="4c36d-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-439">'Identity'</span></span>
- <span data-ttu-id="4c36d-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-441">'Razor'</span></span>
- <span data-ttu-id="4c36d-442">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-444">'Blazor'</span></span>
- <span data-ttu-id="4c36d-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-445">'Identity'</span></span>
- <span data-ttu-id="4c36d-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-447">'Razor'</span></span>
- <span data-ttu-id="4c36d-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-450">'Blazor'</span></span>
- <span data-ttu-id="4c36d-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-451">'Identity'</span></span>
- <span data-ttu-id="4c36d-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-453">'Razor'</span></span>
- <span data-ttu-id="4c36d-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-456">'Blazor'</span></span>
- <span data-ttu-id="4c36d-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-457">'Identity'</span></span>
- <span data-ttu-id="4c36d-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-459">'Razor'</span></span>
- <span data-ttu-id="4c36d-460">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-462">'Blazor'</span></span>
- <span data-ttu-id="4c36d-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-463">'Identity'</span></span>
- <span data-ttu-id="4c36d-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-465">'Razor'</span></span>
- <span data-ttu-id="4c36d-466">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-468">'Blazor'</span></span>
- <span data-ttu-id="4c36d-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-469">'Identity'</span></span>
- <span data-ttu-id="4c36d-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-471">'Razor'</span></span>
- <span data-ttu-id="4c36d-472">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-474">'Blazor'</span></span>
- <span data-ttu-id="4c36d-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-475">'Identity'</span></span>
- <span data-ttu-id="4c36d-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-477">'Razor'</span></span>
- <span data-ttu-id="4c36d-478">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-478">'SignalR' uid:</span></span> 

<span data-ttu-id="4c36d-479">----------------------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="4c36d-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-480">'Blazor'</span></span>
- <span data-ttu-id="4c36d-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-481">'Identity'</span></span>
- <span data-ttu-id="4c36d-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-483">'Razor'</span></span>
- <span data-ttu-id="4c36d-484">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-486">'Blazor'</span></span>
- <span data-ttu-id="4c36d-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-487">'Identity'</span></span>
- <span data-ttu-id="4c36d-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-489">'Razor'</span></span>
- <span data-ttu-id="4c36d-490">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-490">'SignalR' uid:</span></span> 

<span data-ttu-id="4c36d-491">---- | | [Edad](https://tools.ietf.org/html/rfc7234#section-5.1) | Una estimación de la cantidad de tiempo en segundos transcurrido desde que se generó la respuesta o se validó correctamente en el servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="4c36d-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="4c36d-492">| | [Expira](https://tools.ietf.org/html/rfc7234#section-5.3) | Hora a partir de la cual la respuesta se considera obsoleta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="4c36d-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Existe por compatibilidad con versiones anteriores de caché HTTP/1.0 para establecer el `no-cache` comportamiento.</span><span class="sxs-lookup"><span data-stu-id="4c36d-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="4c36d-494">Si el `Cache-Control` encabezado está presente, `Pragma` se omite el encabezado.</span><span class="sxs-lookup"><span data-stu-id="4c36d-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="4c36d-495">| | [Variar](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Especifica que no se debe enviar una respuesta almacenada en caché a menos que todos los `Vary` campos de encabezado coincidan en la solicitud original de la respuesta almacenada en caché y la nueva solicitud.</span><span class="sxs-lookup"><span data-stu-id="4c36d-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="4c36d-496">El almacenamiento en caché basado en HTTP respeta las directivas de control de caché de solicitudes</span><span class="sxs-lookup"><span data-stu-id="4c36d-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="4c36d-497">La [especificación HTTP 1,1 Caching del encabezado Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) requiere una memoria caché para respetar un `Cache-Control` encabezado válido enviado por el cliente.</span><span class="sxs-lookup"><span data-stu-id="4c36d-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="4c36d-498">Un cliente puede realizar solicitudes con un `no-cache` valor de encabezado y obligar al servidor a generar una nueva respuesta para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="4c36d-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="4c36d-499">`Cache-Control`Tener en cuenta siempre los encabezados de solicitud de cliente tiene sentido si tiene en cuenta el objetivo del almacenamiento en caché de http.</span><span class="sxs-lookup"><span data-stu-id="4c36d-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="4c36d-500">En la especificación oficial, el almacenamiento en caché está pensado para reducir la latencia y la sobrecarga de red que supone satisfacer las solicitudes a través de una red de clientes, servidores proxy y servidores.</span><span class="sxs-lookup"><span data-stu-id="4c36d-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="4c36d-501">No es necesariamente una manera de controlar la carga en un servidor de origen.</span><span class="sxs-lookup"><span data-stu-id="4c36d-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="4c36d-502">No hay ningún control de desarrollador sobre este comportamiento de almacenamiento en caché cuando se usa el [middleware de almacenamiento en caché de respuesta](xref:performance/caching/middleware) , ya que el middleware se adhiere a la especificación oficial de almacenamiento en caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="4c36d-503">[Las mejoras planeadas en el middleware son la](https://github.com/dotnet/AspNetCore/issues/2612) oportunidad de configurar el middleware para omitir el encabezado de una solicitud `Cache-Control` al decidir si se va a dar servicio a una respuesta almacenada en caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="4c36d-504">Las mejoras planeadas proporcionan una oportunidad para controlar mejor la carga del servidor.</span><span class="sxs-lookup"><span data-stu-id="4c36d-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="4c36d-505">Otra tecnología de almacenamiento en caché en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c36d-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="4c36d-506">Almacenamiento caché en memoria</span><span class="sxs-lookup"><span data-stu-id="4c36d-506">In-memory caching</span></span>

<span data-ttu-id="4c36d-507">El almacenamiento en caché en memoria utiliza la memoria del servidor para almacenar los datos en caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="4c36d-508">Este tipo de almacenamiento en caché es adecuado para un solo servidor o para varios servidores que usan *sesiones permanentes*.</span><span class="sxs-lookup"><span data-stu-id="4c36d-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="4c36d-509">Las sesiones permanentes significan que las solicitudes de un cliente siempre se enrutan al mismo servidor para su procesamiento.</span><span class="sxs-lookup"><span data-stu-id="4c36d-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="4c36d-510">Para obtener más información, vea <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="4c36d-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="4c36d-511">Caché distribuida</span><span class="sxs-lookup"><span data-stu-id="4c36d-511">Distributed Cache</span></span>

<span data-ttu-id="4c36d-512">Use una caché distribuida para almacenar los datos en memoria cuando la aplicación se hospeda en una granja de servidores o en la nube.</span><span class="sxs-lookup"><span data-stu-id="4c36d-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="4c36d-513">La memoria caché se comparte entre los servidores que procesan las solicitudes.</span><span class="sxs-lookup"><span data-stu-id="4c36d-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="4c36d-514">Un cliente puede enviar una solicitud controlada por cualquier servidor del grupo si están disponibles los datos almacenados en caché del cliente.</span><span class="sxs-lookup"><span data-stu-id="4c36d-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="4c36d-515">ASP.NET Core funciona con memorias caché distribuidas SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)y [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="4c36d-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="4c36d-516">Para obtener más información, vea <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="4c36d-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="4c36d-517">Asistente de etiquetas de caché</span><span class="sxs-lookup"><span data-stu-id="4c36d-517">Cache Tag Helper</span></span>

<span data-ttu-id="4c36d-518">Almacenar en caché el contenido de una vista o una página de MVC Razor con la aplicación auxiliar de etiquetas de caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="4c36d-519">La aplicación auxiliar de etiquetas de caché usa el almacenamiento en caché en memoria para almacenar los datos.</span><span class="sxs-lookup"><span data-stu-id="4c36d-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="4c36d-520">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4c36d-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="4c36d-521">Asistente de etiquetas de caché distribuida</span><span class="sxs-lookup"><span data-stu-id="4c36d-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="4c36d-522">Almacene en caché el contenido de una vista o una Razor Página de MVC en escenarios de granja de servidores web o nube distribuida con la aplicación auxiliar de etiquetas de caché distribuida.</span><span class="sxs-lookup"><span data-stu-id="4c36d-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="4c36d-523">La aplicación auxiliar de etiquetas de caché distribuida usa SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)o [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) para almacenar los datos.</span><span class="sxs-lookup"><span data-stu-id="4c36d-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="4c36d-524">Para obtener más información, vea <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="4c36d-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="4c36d-525">Atributo ResponseCache</span><span class="sxs-lookup"><span data-stu-id="4c36d-525">ResponseCache attribute</span></span>

<span data-ttu-id="4c36d-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Especifica los parámetros necesarios para establecer los encabezados adecuados en el almacenamiento en caché de respuestas.</span><span class="sxs-lookup"><span data-stu-id="4c36d-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="4c36d-527">Deshabilite el almacenamiento en caché para el contenido que contiene información de los clientes autenticados.</span><span class="sxs-lookup"><span data-stu-id="4c36d-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="4c36d-528">El almacenamiento en caché solo debe estar habilitado para el contenido que no cambia en función de la identidad de un usuario o de si un usuario ha iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="4c36d-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="4c36d-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>cambia la respuesta almacenada por los valores de la lista especificada de claves de consulta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="4c36d-530">Cuando se proporciona un valor único de `*` , el middleware varía las respuestas de todos los parámetros de la cadena de consulta de solicitud.</span><span class="sxs-lookup"><span data-stu-id="4c36d-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="4c36d-531">El [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) debe estar habilitado para establecer la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propiedad.</span><span class="sxs-lookup"><span data-stu-id="4c36d-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="4c36d-532">De lo contrario, se produce una excepción en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="4c36d-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="4c36d-533">No hay un encabezado HTTP correspondiente para la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> propiedad.</span><span class="sxs-lookup"><span data-stu-id="4c36d-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="4c36d-534">La propiedad es una característica de HTTP administrada por middleware de almacenamiento en caché de respuestas.</span><span class="sxs-lookup"><span data-stu-id="4c36d-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="4c36d-535">Para que el middleware atienda una respuesta almacenada en caché, la cadena de consulta y el valor de la cadena de consulta deben coincidir con una solicitud anterior.</span><span class="sxs-lookup"><span data-stu-id="4c36d-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="4c36d-536">Por ejemplo, considere la secuencia de solicitudes y los resultados que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="4c36d-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="4c36d-537">Solicitud</span><span class="sxs-lookup"><span data-stu-id="4c36d-537">Request</span></span>                          | <span data-ttu-id="4c36d-538">Resultado</span><span class="sxs-lookup"><span data-stu-id="4c36d-538">Result</span></span>                    |
| ---
<span data-ttu-id="4c36d-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-540">'Blazor'</span></span>
- <span data-ttu-id="4c36d-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-541">'Identity'</span></span>
- <span data-ttu-id="4c36d-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-543">'Razor'</span></span>
- <span data-ttu-id="4c36d-544">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-546">'Blazor'</span></span>
- <span data-ttu-id="4c36d-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-547">'Identity'</span></span>
- <span data-ttu-id="4c36d-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-549">'Razor'</span></span>
- <span data-ttu-id="4c36d-550">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-552">'Blazor'</span></span>
- <span data-ttu-id="4c36d-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-553">'Identity'</span></span>
- <span data-ttu-id="4c36d-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-555">'Razor'</span></span>
- <span data-ttu-id="4c36d-556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-558">'Blazor'</span></span>
- <span data-ttu-id="4c36d-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-559">'Identity'</span></span>
- <span data-ttu-id="4c36d-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-561">'Razor'</span></span>
- <span data-ttu-id="4c36d-562">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-564">'Blazor'</span></span>
- <span data-ttu-id="4c36d-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-565">'Identity'</span></span>
- <span data-ttu-id="4c36d-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-567">'Razor'</span></span>
- <span data-ttu-id="4c36d-568">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-570">'Blazor'</span></span>
- <span data-ttu-id="4c36d-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-571">'Identity'</span></span>
- <span data-ttu-id="4c36d-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-573">'Razor'</span></span>
- <span data-ttu-id="4c36d-574">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-576">'Blazor'</span></span>
- <span data-ttu-id="4c36d-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-577">'Identity'</span></span>
- <span data-ttu-id="4c36d-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-579">'Razor'</span></span>
- <span data-ttu-id="4c36d-580">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-582">'Blazor'</span></span>
- <span data-ttu-id="4c36d-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-583">'Identity'</span></span>
- <span data-ttu-id="4c36d-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-585">'Razor'</span></span>
- <span data-ttu-id="4c36d-586">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-588">'Blazor'</span></span>
- <span data-ttu-id="4c36d-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-589">'Identity'</span></span>
- <span data-ttu-id="4c36d-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-591">'Razor'</span></span>
- <span data-ttu-id="4c36d-592">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-594">'Blazor'</span></span>
- <span data-ttu-id="4c36d-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-595">'Identity'</span></span>
- <span data-ttu-id="4c36d-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-597">'Razor'</span></span>
- <span data-ttu-id="4c36d-598">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-600">'Blazor'</span></span>
- <span data-ttu-id="4c36d-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-601">'Identity'</span></span>
- <span data-ttu-id="4c36d-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-603">'Razor'</span></span>
- <span data-ttu-id="4c36d-604">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-606">'Blazor'</span></span>
- <span data-ttu-id="4c36d-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-607">'Identity'</span></span>
- <span data-ttu-id="4c36d-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-609">'Razor'</span></span>
- <span data-ttu-id="4c36d-610">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-612">'Blazor'</span></span>
- <span data-ttu-id="4c36d-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-613">'Identity'</span></span>
- <span data-ttu-id="4c36d-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-615">'Razor'</span></span>
- <span data-ttu-id="4c36d-616">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-618">'Blazor'</span></span>
- <span data-ttu-id="4c36d-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-619">'Identity'</span></span>
- <span data-ttu-id="4c36d-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-621">'Razor'</span></span>
- <span data-ttu-id="4c36d-622">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-622">'SignalR' uid:</span></span> 

<span data-ttu-id="4c36d-623">---------------- | ---title: Author: Description: monikerRange: MS. Author: MS. Date: no-LOC:</span><span class="sxs-lookup"><span data-stu-id="4c36d-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-624">'Blazor'</span></span>
- <span data-ttu-id="4c36d-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-625">'Identity'</span></span>
- <span data-ttu-id="4c36d-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-627">'Razor'</span></span>
- <span data-ttu-id="4c36d-628">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-630">'Blazor'</span></span>
- <span data-ttu-id="4c36d-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-631">'Identity'</span></span>
- <span data-ttu-id="4c36d-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-633">'Razor'</span></span>
- <span data-ttu-id="4c36d-634">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-636">'Blazor'</span></span>
- <span data-ttu-id="4c36d-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-637">'Identity'</span></span>
- <span data-ttu-id="4c36d-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-639">'Razor'</span></span>
- <span data-ttu-id="4c36d-640">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-642">'Blazor'</span></span>
- <span data-ttu-id="4c36d-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-643">'Identity'</span></span>
- <span data-ttu-id="4c36d-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-645">'Razor'</span></span>
- <span data-ttu-id="4c36d-646">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-648">'Blazor'</span></span>
- <span data-ttu-id="4c36d-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-649">'Identity'</span></span>
- <span data-ttu-id="4c36d-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-651">'Razor'</span></span>
- <span data-ttu-id="4c36d-652">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-654">'Blazor'</span></span>
- <span data-ttu-id="4c36d-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-655">'Identity'</span></span>
- <span data-ttu-id="4c36d-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-657">'Razor'</span></span>
- <span data-ttu-id="4c36d-658">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-660">'Blazor'</span></span>
- <span data-ttu-id="4c36d-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-661">'Identity'</span></span>
- <span data-ttu-id="4c36d-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-663">'Razor'</span></span>
- <span data-ttu-id="4c36d-664">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-666">'Blazor'</span></span>
- <span data-ttu-id="4c36d-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-667">'Identity'</span></span>
- <span data-ttu-id="4c36d-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-669">'Razor'</span></span>
- <span data-ttu-id="4c36d-670">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-672">'Blazor'</span></span>
- <span data-ttu-id="4c36d-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-673">'Identity'</span></span>
- <span data-ttu-id="4c36d-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-675">'Razor'</span></span>
- <span data-ttu-id="4c36d-676">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4c36d-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4c36d-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4c36d-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-678">'Blazor'</span></span>
- <span data-ttu-id="4c36d-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4c36d-679">'Identity'</span></span>
- <span data-ttu-id="4c36d-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4c36d-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="4c36d-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4c36d-681">'Razor'</span></span>
- <span data-ttu-id="4c36d-682">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4c36d-682">'SignalR' uid:</span></span> 

<span data-ttu-id="4c36d-683">------------- | | `http://example.com?key1=value1` | Se devuelve desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="4c36d-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="4c36d-684">| | `http://example.com?key1=value1` | Se devuelve desde middleware.</span><span class="sxs-lookup"><span data-stu-id="4c36d-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="4c36d-685">| | `http://example.com?key1=value2` | Se devuelve desde el servidor.</span><span class="sxs-lookup"><span data-stu-id="4c36d-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="4c36d-686">El servidor devuelve la primera solicitud y la almacena en caché en middleware.</span><span class="sxs-lookup"><span data-stu-id="4c36d-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="4c36d-687">El middleware devuelve la segunda solicitud porque la cadena de consulta coincide con la solicitud anterior.</span><span class="sxs-lookup"><span data-stu-id="4c36d-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="4c36d-688">La tercera solicitud no está en la caché de middleware porque el valor de la cadena de consulta no coincide con una solicitud anterior.</span><span class="sxs-lookup"><span data-stu-id="4c36d-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="4c36d-689"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Se utiliza para configurar y crear (mediante <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) un `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="4c36d-690">El `ResponseCacheFilter` realiza el trabajo de actualizar los encabezados HTTP y las características correspondientes de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="4c36d-691">El filtro:</span><span class="sxs-lookup"><span data-stu-id="4c36d-691">The filter:</span></span>

* <span data-ttu-id="4c36d-692">Quita todos los encabezados existentes para `Vary` , `Cache-Control` y `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="4c36d-693">Escribe los encabezados adecuados en función de las propiedades establecidas en <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="4c36d-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="4c36d-694">Actualiza la característica HTTP de almacenamiento en caché de respuestas si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> está establecida.</span><span class="sxs-lookup"><span data-stu-id="4c36d-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="4c36d-695">Variaciones</span><span class="sxs-lookup"><span data-stu-id="4c36d-695">Vary</span></span>

<span data-ttu-id="4c36d-696">Este encabezado solo se escribe cuando <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> se establece la propiedad.</span><span class="sxs-lookup"><span data-stu-id="4c36d-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="4c36d-697">Propiedad establecida en el `Vary` valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="4c36d-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="4c36d-698">En el ejemplo siguiente se usa la <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> propiedad:</span><span class="sxs-lookup"><span data-stu-id="4c36d-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="4c36d-699">Use la aplicación de ejemplo para ver los encabezados de respuesta con las herramientas de red del explorador.</span><span class="sxs-lookup"><span data-stu-id="4c36d-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="4c36d-700">Los siguientes encabezados de respuesta se envían con la respuesta de la página Cache1:</span><span class="sxs-lookup"><span data-stu-id="4c36d-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="4c36d-701">Nostore y Location. None</span><span class="sxs-lookup"><span data-stu-id="4c36d-701">NoStore and Location.None</span></span>

<span data-ttu-id="4c36d-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>invalida la mayoría de las demás propiedades.</span><span class="sxs-lookup"><span data-stu-id="4c36d-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="4c36d-703">Cuando esta propiedad se establece en `true` , el `Cache-Control` encabezado se establece en `no-store` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="4c36d-704">Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> está establecido en `None` :</span><span class="sxs-lookup"><span data-stu-id="4c36d-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="4c36d-705">`Cache-Control` se establece en `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="4c36d-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="4c36d-706">`Pragma` se establece en `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="4c36d-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="4c36d-707">Si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> es `false` y <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> es `None` , `Cache-Control` y `Pragma` se establecen en `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="4c36d-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>normalmente se establece en `true` para las páginas de error.</span><span class="sxs-lookup"><span data-stu-id="4c36d-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="4c36d-709">La página Cache2 de la aplicación de ejemplo genera encabezados de respuesta que indican al cliente que no almacene la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="4c36d-710">La aplicación de ejemplo devuelve la página Cache2 con los encabezados siguientes:</span><span class="sxs-lookup"><span data-stu-id="4c36d-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="4c36d-711">Ubicación y duración</span><span class="sxs-lookup"><span data-stu-id="4c36d-711">Location and Duration</span></span>

<span data-ttu-id="4c36d-712">Para habilitar el almacenamiento en caché, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> debe establecerse en un valor positivo y <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> debe ser `Any` (el valor predeterminado) o `Client` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="4c36d-713">El marco de trabajo establece el `Cache-Control` encabezado en el valor de ubicación seguido del `max-age` de la respuesta.</span><span class="sxs-lookup"><span data-stu-id="4c36d-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="4c36d-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>las opciones de `Any` y `Client` traducen en `Cache-Control` valores de encabezado de `public` y `private` , respectivamente.</span><span class="sxs-lookup"><span data-stu-id="4c36d-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="4c36d-715">Como se indicó en la sección [nostore y Location. None](#nostore-and-locationnone) , si <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> se establece en, `None` los `Cache-Control` encabezados y se establecen en `Pragma` `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="4c36d-716">`Location.Any`( `Cache-Control` establecido en `public` ) indica que el *cliente o cualquier proxy intermedio* puede almacenar en caché el valor, incluido el [middleware de almacenamiento en](xref:performance/caching/middleware)caché de las respuestas.</span><span class="sxs-lookup"><span data-stu-id="4c36d-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="4c36d-717">`Location.Client`( `Cache-Control` establecido en `private` ) indica que *solo el cliente* puede almacenar en caché el valor.</span><span class="sxs-lookup"><span data-stu-id="4c36d-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="4c36d-718">Ninguna caché intermedia debe almacenar en caché el valor, incluido el [middleware de almacenamiento en](xref:performance/caching/middleware)caché de las respuestas.</span><span class="sxs-lookup"><span data-stu-id="4c36d-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="4c36d-719">Los encabezados de control de caché simplemente proporcionan instrucciones a los clientes y los proxies intermediarios cuando y cómo almacenar en caché las respuestas.</span><span class="sxs-lookup"><span data-stu-id="4c36d-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="4c36d-720">No hay ninguna garantía de que los clientes y los servidores proxy cumplan la [especificación de almacenamiento en caché HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="4c36d-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="4c36d-721">El [middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) sigue siempre las reglas de almacenamiento en caché establecidas por la especificación.</span><span class="sxs-lookup"><span data-stu-id="4c36d-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="4c36d-722">En el ejemplo siguiente se muestra el modelo de página Cache3 de la aplicación de ejemplo y los encabezados generados estableciendo <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> y saliendo del <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> valor predeterminado:</span><span class="sxs-lookup"><span data-stu-id="4c36d-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="4c36d-723">La aplicación de ejemplo devuelve la página Cache3 con el siguiente encabezado:</span><span class="sxs-lookup"><span data-stu-id="4c36d-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="4c36d-724">Perfiles de caché</span><span class="sxs-lookup"><span data-stu-id="4c36d-724">Cache profiles</span></span>

<span data-ttu-id="4c36d-725">En lugar de duplicar la configuración de la caché de respuestas en muchos atributos de acción del controlador, los perfiles de caché se pueden configurar como opciones al configurar MVC/ Razor páginas en `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4c36d-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4c36d-726">Los valores que se encuentran en un perfil de caché al que se hace referencia se utilizan como valores predeterminados de <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> y se reemplazan por las propiedades especificadas en el atributo.</span><span class="sxs-lookup"><span data-stu-id="4c36d-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="4c36d-727">Configure un perfil de caché.</span><span class="sxs-lookup"><span data-stu-id="4c36d-727">Set up a cache profile.</span></span> <span data-ttu-id="4c36d-728">En el ejemplo siguiente se muestra un perfil de caché de 30 segundos en la aplicación de ejemplo `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4c36d-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4c36d-729">El modelo de página Cache4 de la aplicación de ejemplo hace referencia al `Default30` Perfil de caché:</span><span class="sxs-lookup"><span data-stu-id="4c36d-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="4c36d-730"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Se puede aplicar a:</span><span class="sxs-lookup"><span data-stu-id="4c36d-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="4c36d-731">Controladores de páginas (clases): los atributos no se pueden aplicar a los métodos de controlador.</span><span class="sxs-lookup"><span data-stu-id="4c36d-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="4c36d-732">Controladores MVC (clases).</span><span class="sxs-lookup"><span data-stu-id="4c36d-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="4c36d-733">Acciones de MVC (métodos): los atributos de nivel de método reemplazan a los valores especificados en los atributos de nivel de clase.</span><span class="sxs-lookup"><span data-stu-id="4c36d-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="4c36d-734">El encabezado resultante aplicado a la respuesta de la página Cache4 por el `Default30` Perfil de caché:</span><span class="sxs-lookup"><span data-stu-id="4c36d-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="4c36d-735">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="4c36d-735">Additional resources</span></span>

* [<span data-ttu-id="4c36d-736">Almacenar respuestas en cachés</span><span class="sxs-lookup"><span data-stu-id="4c36d-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="4c36d-737">Cache-control</span><span class="sxs-lookup"><span data-stu-id="4c36d-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
