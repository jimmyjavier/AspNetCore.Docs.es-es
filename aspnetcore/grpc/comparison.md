---
<span data-ttu-id="64bfd-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-102">'Blazor'</span></span>
- <span data-ttu-id="64bfd-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-103">'Identity'</span></span>
- <span data-ttu-id="64bfd-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-105">'Razor'</span></span>
- <span data-ttu-id="64bfd-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="64bfd-107">Comparación entre los servicios gRPC y las API HTTP</span><span class="sxs-lookup"><span data-stu-id="64bfd-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="64bfd-108">Por [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="64bfd-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="64bfd-109">En este artículo se realiza una comparación entre los [servicios gRPC](https://grpc.io/docs/guides/) y las API HTTP con JSON (incluidas las [API web](xref:web-api/index) de ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="64bfd-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="64bfd-110">La tecnología que se usa para proporcionar una API para la aplicación es una decisión importante, y gRPC ofrece ventajas exclusivas en comparación con las API HTTP.</span><span class="sxs-lookup"><span data-stu-id="64bfd-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="64bfd-111">En este artículo se describen las ventajas y los inconvenientes de gRPC, y se recomiendan escenarios para usar gRPC antes que otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="64bfd-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="64bfd-112">Comparación general</span><span class="sxs-lookup"><span data-stu-id="64bfd-112">High-level comparison</span></span>

<span data-ttu-id="64bfd-113">En la tabla siguiente se ofrece una comparación general de las características entre gRPC y las API HTTP con JSON.</span><span class="sxs-lookup"><span data-stu-id="64bfd-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="64bfd-114">Característica</span><span class="sxs-lookup"><span data-stu-id="64bfd-114">Feature</span></span>          | <span data-ttu-id="64bfd-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="64bfd-115">gRPC</span></span>                                               | <span data-ttu-id="64bfd-116">API HTTP con JSON</span><span class="sxs-lookup"><span data-stu-id="64bfd-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="64bfd-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-118">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-118">'Blazor'</span></span>
- <span data-ttu-id="64bfd-119">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-119">'Identity'</span></span>
- <span data-ttu-id="64bfd-120">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-121">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-121">'Razor'</span></span>
- <span data-ttu-id="64bfd-122">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-124">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-124">'Blazor'</span></span>
- <span data-ttu-id="64bfd-125">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-125">'Identity'</span></span>
- <span data-ttu-id="64bfd-126">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-127">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-127">'Razor'</span></span>
- <span data-ttu-id="64bfd-128">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-130">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-130">'Blazor'</span></span>
- <span data-ttu-id="64bfd-131">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-131">'Identity'</span></span>
- <span data-ttu-id="64bfd-132">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-133">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-133">'Razor'</span></span>
- <span data-ttu-id="64bfd-134">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-136">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-136">'Blazor'</span></span>
- <span data-ttu-id="64bfd-137">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-137">'Identity'</span></span>
- <span data-ttu-id="64bfd-138">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-139">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-139">'Razor'</span></span>
- <span data-ttu-id="64bfd-140">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-142">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-142">'Blazor'</span></span>
- <span data-ttu-id="64bfd-143">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-143">'Identity'</span></span>
- <span data-ttu-id="64bfd-144">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-145">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-145">'Razor'</span></span>
- <span data-ttu-id="64bfd-146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-148">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-148">'Blazor'</span></span>
- <span data-ttu-id="64bfd-149">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-149">'Identity'</span></span>
- <span data-ttu-id="64bfd-150">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-151">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-151">'Razor'</span></span>
- <span data-ttu-id="64bfd-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-152">'SignalR' uid:</span></span> 

<span data-ttu-id="64bfd-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-154">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-154">'Blazor'</span></span>
- <span data-ttu-id="64bfd-155">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-155">'Identity'</span></span>
- <span data-ttu-id="64bfd-156">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-157">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-157">'Razor'</span></span>
- <span data-ttu-id="64bfd-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-160">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-160">'Blazor'</span></span>
- <span data-ttu-id="64bfd-161">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-161">'Identity'</span></span>
- <span data-ttu-id="64bfd-162">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-163">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-163">'Razor'</span></span>
- <span data-ttu-id="64bfd-164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-166">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-166">'Blazor'</span></span>
- <span data-ttu-id="64bfd-167">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-167">'Identity'</span></span>
- <span data-ttu-id="64bfd-168">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-169">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-169">'Razor'</span></span>
- <span data-ttu-id="64bfd-170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-172">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-172">'Blazor'</span></span>
- <span data-ttu-id="64bfd-173">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-173">'Identity'</span></span>
- <span data-ttu-id="64bfd-174">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-175">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-175">'Razor'</span></span>
- <span data-ttu-id="64bfd-176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-178">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-178">'Blazor'</span></span>
- <span data-ttu-id="64bfd-179">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-179">'Identity'</span></span>
- <span data-ttu-id="64bfd-180">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-181">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-181">'Razor'</span></span>
- <span data-ttu-id="64bfd-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-184">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-184">'Blazor'</span></span>
- <span data-ttu-id="64bfd-185">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-185">'Identity'</span></span>
- <span data-ttu-id="64bfd-186">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-187">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-187">'Razor'</span></span>
- <span data-ttu-id="64bfd-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-190">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-190">'Blazor'</span></span>
- <span data-ttu-id="64bfd-191">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-191">'Identity'</span></span>
- <span data-ttu-id="64bfd-192">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-193">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-193">'Razor'</span></span>
- <span data-ttu-id="64bfd-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-196">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-196">'Blazor'</span></span>
- <span data-ttu-id="64bfd-197">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-197">'Identity'</span></span>
- <span data-ttu-id="64bfd-198">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-199">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-199">'Razor'</span></span>
- <span data-ttu-id="64bfd-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-202">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-202">'Blazor'</span></span>
- <span data-ttu-id="64bfd-203">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-203">'Identity'</span></span>
- <span data-ttu-id="64bfd-204">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-205">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-205">'Razor'</span></span>
- <span data-ttu-id="64bfd-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-208">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-208">'Blazor'</span></span>
- <span data-ttu-id="64bfd-209">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-209">'Identity'</span></span>
- <span data-ttu-id="64bfd-210">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-211">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-211">'Razor'</span></span>
- <span data-ttu-id="64bfd-212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-214">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-214">'Blazor'</span></span>
- <span data-ttu-id="64bfd-215">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-215">'Identity'</span></span>
- <span data-ttu-id="64bfd-216">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-217">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-217">'Razor'</span></span>
- <span data-ttu-id="64bfd-218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-220">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-220">'Blazor'</span></span>
- <span data-ttu-id="64bfd-221">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-221">'Identity'</span></span>
- <span data-ttu-id="64bfd-222">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-223">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-223">'Razor'</span></span>
- <span data-ttu-id="64bfd-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-226">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-226">'Blazor'</span></span>
- <span data-ttu-id="64bfd-227">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-227">'Identity'</span></span>
- <span data-ttu-id="64bfd-228">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-229">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-229">'Razor'</span></span>
- <span data-ttu-id="64bfd-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-232">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-232">'Blazor'</span></span>
- <span data-ttu-id="64bfd-233">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-233">'Identity'</span></span>
- <span data-ttu-id="64bfd-234">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-235">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-235">'Razor'</span></span>
- <span data-ttu-id="64bfd-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-238">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-238">'Blazor'</span></span>
- <span data-ttu-id="64bfd-239">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-239">'Identity'</span></span>
- <span data-ttu-id="64bfd-240">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-241">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-241">'Razor'</span></span>
- <span data-ttu-id="64bfd-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-244">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-244">'Blazor'</span></span>
- <span data-ttu-id="64bfd-245">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-245">'Identity'</span></span>
- <span data-ttu-id="64bfd-246">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-247">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-247">'Razor'</span></span>
- <span data-ttu-id="64bfd-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-250">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-250">'Blazor'</span></span>
- <span data-ttu-id="64bfd-251">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-251">'Identity'</span></span>
- <span data-ttu-id="64bfd-252">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-253">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-253">'Razor'</span></span>
- <span data-ttu-id="64bfd-254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-256">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-256">'Blazor'</span></span>
- <span data-ttu-id="64bfd-257">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-257">'Identity'</span></span>
- <span data-ttu-id="64bfd-258">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-259">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-259">'Razor'</span></span>
- <span data-ttu-id="64bfd-260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-262">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-262">'Blazor'</span></span>
- <span data-ttu-id="64bfd-263">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-263">'Identity'</span></span>
- <span data-ttu-id="64bfd-264">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-265">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-265">'Razor'</span></span>
- <span data-ttu-id="64bfd-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-268">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-268">'Blazor'</span></span>
- <span data-ttu-id="64bfd-269">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-269">'Identity'</span></span>
- <span data-ttu-id="64bfd-270">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-271">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-271">'Razor'</span></span>
- <span data-ttu-id="64bfd-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-274">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-274">'Blazor'</span></span>
- <span data-ttu-id="64bfd-275">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-275">'Identity'</span></span>
- <span data-ttu-id="64bfd-276">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-277">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-277">'Razor'</span></span>
- <span data-ttu-id="64bfd-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-280">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-280">'Blazor'</span></span>
- <span data-ttu-id="64bfd-281">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-281">'Identity'</span></span>
- <span data-ttu-id="64bfd-282">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-283">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-283">'Razor'</span></span>
- <span data-ttu-id="64bfd-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-286">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-286">'Blazor'</span></span>
- <span data-ttu-id="64bfd-287">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-287">'Identity'</span></span>
- <span data-ttu-id="64bfd-288">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-289">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-289">'Razor'</span></span>
- <span data-ttu-id="64bfd-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-290">'SignalR' uid:</span></span> 

<span data-ttu-id="64bfd-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-292">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-292">'Blazor'</span></span>
- <span data-ttu-id="64bfd-293">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-293">'Identity'</span></span>
- <span data-ttu-id="64bfd-294">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-295">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-295">'Razor'</span></span>
- <span data-ttu-id="64bfd-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-298">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-298">'Blazor'</span></span>
- <span data-ttu-id="64bfd-299">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-299">'Identity'</span></span>
- <span data-ttu-id="64bfd-300">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-301">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-301">'Razor'</span></span>
- <span data-ttu-id="64bfd-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-304">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-304">'Blazor'</span></span>
- <span data-ttu-id="64bfd-305">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-305">'Identity'</span></span>
- <span data-ttu-id="64bfd-306">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-307">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-307">'Razor'</span></span>
- <span data-ttu-id="64bfd-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-310">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-310">'Blazor'</span></span>
- <span data-ttu-id="64bfd-311">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-311">'Identity'</span></span>
- <span data-ttu-id="64bfd-312">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-313">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-313">'Razor'</span></span>
- <span data-ttu-id="64bfd-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-316">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-316">'Blazor'</span></span>
- <span data-ttu-id="64bfd-317">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-317">'Identity'</span></span>
- <span data-ttu-id="64bfd-318">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-319">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-319">'Razor'</span></span>
- <span data-ttu-id="64bfd-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-322">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-322">'Blazor'</span></span>
- <span data-ttu-id="64bfd-323">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-323">'Identity'</span></span>
- <span data-ttu-id="64bfd-324">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-325">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-325">'Razor'</span></span>
- <span data-ttu-id="64bfd-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-328">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-328">'Blazor'</span></span>
- <span data-ttu-id="64bfd-329">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-329">'Identity'</span></span>
- <span data-ttu-id="64bfd-330">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-331">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-331">'Razor'</span></span>
- <span data-ttu-id="64bfd-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-334">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-334">'Blazor'</span></span>
- <span data-ttu-id="64bfd-335">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-335">'Identity'</span></span>
- <span data-ttu-id="64bfd-336">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-337">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-337">'Razor'</span></span>
- <span data-ttu-id="64bfd-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-340">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-340">'Blazor'</span></span>
- <span data-ttu-id="64bfd-341">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-341">'Identity'</span></span>
- <span data-ttu-id="64bfd-342">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-343">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-343">'Razor'</span></span>
- <span data-ttu-id="64bfd-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-346">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-346">'Blazor'</span></span>
- <span data-ttu-id="64bfd-347">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-347">'Identity'</span></span>
- <span data-ttu-id="64bfd-348">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-349">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-349">'Razor'</span></span>
- <span data-ttu-id="64bfd-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-352">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-352">'Blazor'</span></span>
- <span data-ttu-id="64bfd-353">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-353">'Identity'</span></span>
- <span data-ttu-id="64bfd-354">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-355">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-355">'Razor'</span></span>
- <span data-ttu-id="64bfd-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="64bfd-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="64bfd-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="64bfd-358">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-358">'Blazor'</span></span>
- <span data-ttu-id="64bfd-359">"Identity"</span><span class="sxs-lookup"><span data-stu-id="64bfd-359">'Identity'</span></span>
- <span data-ttu-id="64bfd-360">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="64bfd-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="64bfd-361">"Razor"</span><span class="sxs-lookup"><span data-stu-id="64bfd-361">'Razor'</span></span>
- <span data-ttu-id="64bfd-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="64bfd-362">'SignalR' uid:</span></span> 

<span data-ttu-id="64bfd-363">--------------- | | Contrato         | Obligatorio ( *.proto*)                                | Opcional (OpenAPI)            | | Protocolo         | HTTP/2                                             | HTTP                          | | Carga útil          | [Protobuf (pequeño, binario)](#performance)           | JSON (grande, legible para usuarios)  | | Carácter preceptivo | [Especificación estricta](#strict-specification)      | Flexible.</span><span class="sxs-lookup"><span data-stu-id="64bfd-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="64bfd-364">Cualquier HTTP es válido.</span><span class="sxs-lookup"><span data-stu-id="64bfd-364">Any HTTP is valid.</span></span>     <span data-ttu-id="64bfd-365">| | Streaming        | [Cliente, servidor, bidireccional](#streaming)       | Cliente, servidor                | | Compatibilidad con exploradores  | [No (requiere grpc-web)](#limited-browser-support) | Sí                           | | Seguridad         | Transporte (TLS)                                    | Transporte (TLS)               | | Generación de código de cliente | [Sí](#code-generation)                      | OpenAPI + herramientas de terceros |</span><span class="sxs-lookup"><span data-stu-id="64bfd-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="64bfd-366">Ventajas de gRPC</span><span class="sxs-lookup"><span data-stu-id="64bfd-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="64bfd-367">Rendimiento</span><span class="sxs-lookup"><span data-stu-id="64bfd-367">Performance</span></span>

<span data-ttu-id="64bfd-368">Los mensajes de gRPC se serializan mediante [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), un formato de mensaje binario eficaz.</span><span class="sxs-lookup"><span data-stu-id="64bfd-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="64bfd-369">Protobuf se serializa muy rápidamente en el servidor y el cliente.</span><span class="sxs-lookup"><span data-stu-id="64bfd-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="64bfd-370">La serialización de Protobuf genera cargas de mensajes pequeñas, un aspecto importante en escenarios de ancho de banda limitado como las aplicaciones móviles.</span><span class="sxs-lookup"><span data-stu-id="64bfd-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="64bfd-371">gRPC está diseñado para HTTP/2, una revisión principal de HTTP que proporciona importantes ventajas de rendimiento con respecto a HTTP 1.x:</span><span class="sxs-lookup"><span data-stu-id="64bfd-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="64bfd-372">Tramas binarias y compresión.</span><span class="sxs-lookup"><span data-stu-id="64bfd-372">Binary framing and compression.</span></span> <span data-ttu-id="64bfd-373">El protocolo HTTP/2 es compacto y eficaz tanto para el envío como para la recepción.</span><span class="sxs-lookup"><span data-stu-id="64bfd-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="64bfd-374">Multiplexación de varias llamadas HTTP/2 a través de una única conexión TCP.</span><span class="sxs-lookup"><span data-stu-id="64bfd-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="64bfd-375">La multiplexación elimina el [bloqueo de encabezado de línea](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="64bfd-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="64bfd-376">HTTP/2 no es exclusivo de gRPC.</span><span class="sxs-lookup"><span data-stu-id="64bfd-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="64bfd-377">Muchos tipos de solicitud, incluidas las API HTTP con JSON, pueden usar HTTP/2 y beneficiarse de sus mejoras de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="64bfd-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="64bfd-378">Generación de código</span><span class="sxs-lookup"><span data-stu-id="64bfd-378">Code generation</span></span>

<span data-ttu-id="64bfd-379">Todos los marcos de trabajo de gRPC proporcionan compatibilidad de primera clase con la generación de código.</span><span class="sxs-lookup"><span data-stu-id="64bfd-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="64bfd-380">Un archivo principal para el desarrollo de gRPC es [.proto](https://developers.google.com/protocol-buffers/docs/proto3), en el que se define el contrato de servicios y mensajes de gRPC.</span><span class="sxs-lookup"><span data-stu-id="64bfd-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="64bfd-381">A partir de este archivo, los marcos gRPC generarán el código de una clase base de servicio, mensajes y un cliente completo.</span><span class="sxs-lookup"><span data-stu-id="64bfd-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="64bfd-382">Mediante el uso compartido del archivo *.proto* entre el servidor y el cliente, los mensajes y el código de cliente se pueden generar de extremo a extremo.</span><span class="sxs-lookup"><span data-stu-id="64bfd-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="64bfd-383">La generación de código del cliente elimina la duplicación de mensajes en el cliente y el servidor, y crea de forma automática un cliente fuertemente tipado.</span><span class="sxs-lookup"><span data-stu-id="64bfd-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="64bfd-384">Al no tener que escribir un cliente se ahorra considerablemente tiempo de desarrollo en las aplicaciones con muchos servicios.</span><span class="sxs-lookup"><span data-stu-id="64bfd-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="64bfd-385">Especificación estricta</span><span class="sxs-lookup"><span data-stu-id="64bfd-385">Strict specification</span></span>

<span data-ttu-id="64bfd-386">No existe una especificación formal para la API HTTP con JSON.</span><span class="sxs-lookup"><span data-stu-id="64bfd-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="64bfd-387">Los desarrolladores debaten el mejor formato de las direcciones URL, los verbos HTTP y los códigos de respuesta.</span><span class="sxs-lookup"><span data-stu-id="64bfd-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="64bfd-388">La [especificación gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) es preceptiva con respecto al formato que debe seguir un servicio gRPC.</span><span class="sxs-lookup"><span data-stu-id="64bfd-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="64bfd-389">gRPC elimina el debate y ahorra tiempo de desarrollo, ya que es coherente entre las plataformas y las implementaciones.</span><span class="sxs-lookup"><span data-stu-id="64bfd-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="64bfd-390">Streaming</span><span class="sxs-lookup"><span data-stu-id="64bfd-390">Streaming</span></span>

<span data-ttu-id="64bfd-391">HTTP/2 proporciona una base para las secuencias de comunicación en tiempo real de larga duración.</span><span class="sxs-lookup"><span data-stu-id="64bfd-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="64bfd-392">gRPC proporciona compatibilidad de primera clase para el streaming a través de HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="64bfd-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="64bfd-393">Un servicio gRPC admite todas las combinaciones de streaming:</span><span class="sxs-lookup"><span data-stu-id="64bfd-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="64bfd-394">Unario (sin streaming)</span><span class="sxs-lookup"><span data-stu-id="64bfd-394">Unary (no streaming)</span></span>
* <span data-ttu-id="64bfd-395">Streaming del servidor al cliente</span><span class="sxs-lookup"><span data-stu-id="64bfd-395">Server to client streaming</span></span>
* <span data-ttu-id="64bfd-396">Streaming del cliente al servidor</span><span class="sxs-lookup"><span data-stu-id="64bfd-396">Client to server streaming</span></span>
* <span data-ttu-id="64bfd-397">Streaming bidireccional</span><span class="sxs-lookup"><span data-stu-id="64bfd-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="64bfd-398">Fecha límite o tiempos de expiración y cancelación</span><span class="sxs-lookup"><span data-stu-id="64bfd-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="64bfd-399">gRPC permite a los clientes especificar cuánto tiempo están dispuestos a esperar a que se complete una RPC.</span><span class="sxs-lookup"><span data-stu-id="64bfd-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="64bfd-400">La [fecha límite](https://grpc.io/blog/deadlines) se envía al servidor y este puede decidir qué acción realizar si supera la fecha límite.</span><span class="sxs-lookup"><span data-stu-id="64bfd-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="64bfd-401">Por ejemplo, es posible que el servidor cancele las solicitudes de gRPC, HTTP o base de datos en curso si se alcanza el tiempo de expiración.</span><span class="sxs-lookup"><span data-stu-id="64bfd-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="64bfd-402">La propagación de la fecha límite y la cancelación mediante llamadas secundarias de gRPC ayuda a aplicar los límites de uso de recursos.</span><span class="sxs-lookup"><span data-stu-id="64bfd-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="64bfd-403">Escenarios recomendados de gRPC</span><span class="sxs-lookup"><span data-stu-id="64bfd-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="64bfd-404">gRPC se adapta perfectamente a los escenarios siguientes:</span><span class="sxs-lookup"><span data-stu-id="64bfd-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="64bfd-405">**Microservicios**: gRPC está diseñado para la comunicación de baja latencia y rendimiento alto.</span><span class="sxs-lookup"><span data-stu-id="64bfd-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="64bfd-406">gRPC resulta idóneo para microservicios ligeros en los que la eficiencia sea crítica.</span><span class="sxs-lookup"><span data-stu-id="64bfd-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="64bfd-407">**Comunicación punto a punto en tiempo real**: gRPC tiene una excelente compatibilidad con el streaming bidireccional.</span><span class="sxs-lookup"><span data-stu-id="64bfd-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="64bfd-408">Los servicios gRPC pueden insertar mensajes en tiempo real sin sondeos.</span><span class="sxs-lookup"><span data-stu-id="64bfd-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="64bfd-409">**Entornos políglotas**: las herramientas de gRPC admiten todos los lenguajes de desarrollo más populares, lo que convierte a gRPC en una buena opción para entornos de varios lenguajes.</span><span class="sxs-lookup"><span data-stu-id="64bfd-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="64bfd-410">**Entornos restringidos de red**: los mensajes gRPC se serializan con Protobuf, un formato de mensaje ligero.</span><span class="sxs-lookup"><span data-stu-id="64bfd-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="64bfd-411">Un mensaje gRPC siempre es más pequeño que un mensaje JSON equivalente.</span><span class="sxs-lookup"><span data-stu-id="64bfd-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="64bfd-412">Inconvenientes de gRPC</span><span class="sxs-lookup"><span data-stu-id="64bfd-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="64bfd-413">Compatibilidad limitada con exploradores</span><span class="sxs-lookup"><span data-stu-id="64bfd-413">Limited browser support</span></span>

<span data-ttu-id="64bfd-414">En la actualidad no es posible llamar directamente a un servicio gRPC desde un explorador.</span><span class="sxs-lookup"><span data-stu-id="64bfd-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="64bfd-415">gRPC usa intensamente características de HTTP/2 y ningún explorador proporciona el nivel de control requerido a través de solicitudes web para admitir un cliente de gRPC.</span><span class="sxs-lookup"><span data-stu-id="64bfd-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="64bfd-416">Por ejemplo, los exploradores no permiten que el autor de la llamada exija el uso de HTTP/2, ni proporcionan acceso a los marcos HTTP/2 subyacentes.</span><span class="sxs-lookup"><span data-stu-id="64bfd-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="64bfd-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) es una tecnología adicional del equipo de gRPC que proporciona compatibilidad limitada con gRPC en el explorador.</span><span class="sxs-lookup"><span data-stu-id="64bfd-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="64bfd-418">gRPC-Web consta de dos partes: un cliente de JavaScript que admite todos los exploradores modernos y un proxy gRPC-Web en el servidor.</span><span class="sxs-lookup"><span data-stu-id="64bfd-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="64bfd-419">El cliente gRPC-Web llama al proxy y este reenviará las solicitudes a gRPC al servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="64bfd-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="64bfd-420">No todas las características de gRPC son compatibles con gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="64bfd-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="64bfd-421">No se admite el streaming de cliente y bidireccional, y existe compatibilidad limitada para el streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="64bfd-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="64bfd-422">.NET Core tiene compatibilidad experimental para gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="64bfd-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="64bfd-423">Visite <xref:grpc/browser> para más información.</span><span class="sxs-lookup"><span data-stu-id="64bfd-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="64bfd-424">No es legible por el usuario</span><span class="sxs-lookup"><span data-stu-id="64bfd-424">Not human readable</span></span>

<span data-ttu-id="64bfd-425">Las solicitudes de API HTTP se envían como texto y se pueden leer y crear por parte de los usuarios.</span><span class="sxs-lookup"><span data-stu-id="64bfd-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="64bfd-426">De forma predeterminada, los mensajes gRPC se codifican con Protobuf.</span><span class="sxs-lookup"><span data-stu-id="64bfd-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="64bfd-427">Aunque Protobuf es eficaz para el envío y la recepción, su formato binario no es legible.</span><span class="sxs-lookup"><span data-stu-id="64bfd-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="64bfd-428">Protobuf requiere la descripción de la interfaz del mensaje especificada en el archivo *.proto* para deserializarse correctamente.</span><span class="sxs-lookup"><span data-stu-id="64bfd-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="64bfd-429">Se requieren herramientas adicionales para analizar las cargas de Protobuf en la conexión y redactar las solicitudes a mano.</span><span class="sxs-lookup"><span data-stu-id="64bfd-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="64bfd-430">Existen características como la [reflexión del servidor](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) y la [herramienta de línea de comandos de gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) para ayudar con los mensajes de Protobuf binarios.</span><span class="sxs-lookup"><span data-stu-id="64bfd-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="64bfd-431">Además, los mensajes de Protobuf admiten [la conversión a y desde JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span><span class="sxs-lookup"><span data-stu-id="64bfd-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="64bfd-432">La conversión de JSON integrada proporciona una manera eficaz de convertir los mensajes de Protobuf a y desde formato legible durante la depuración.</span><span class="sxs-lookup"><span data-stu-id="64bfd-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="64bfd-433">Escenarios de marcos alternativos</span><span class="sxs-lookup"><span data-stu-id="64bfd-433">Alternative framework scenarios</span></span>

<span data-ttu-id="64bfd-434">En los escenarios siguientes se recomiendan otros marcos de trabajo antes que gRPC:</span><span class="sxs-lookup"><span data-stu-id="64bfd-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="64bfd-435">**API accesibles de explorador**: gRPC no se admite de forma completa en el explorador.</span><span class="sxs-lookup"><span data-stu-id="64bfd-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="64bfd-436">gRPC-Web puede ofrecer compatibilidad con el explorador, pero tiene limitaciones e introduce un proxy de servidor.</span><span class="sxs-lookup"><span data-stu-id="64bfd-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="64bfd-437">**Retransmisión de la comunicación en tiempo real**: gRPC admite la comunicación en tiempo real a través de streaming, pero no existe el concepto de retransmisión de un mensaje a las conexiones registradas.</span><span class="sxs-lookup"><span data-stu-id="64bfd-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="64bfd-438">Por ejemplo, en un escenario de salón de chat en el que se deben enviar nuevos mensajes de chat a todos los clientes, es necesario que cada llamada gRPC transmita de forma individual los nuevos mensajes de chat al cliente.</span><span class="sxs-lookup"><span data-stu-id="64bfd-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="64bfd-439">[SignalR](xref:signalr/introduction) es un marco útil para este escenario.</span><span class="sxs-lookup"><span data-stu-id="64bfd-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> SignalR<span data-ttu-id="64bfd-440"> tiene el concepto de conexiones persistentes y compatibilidad integrada para la retransmisión de mensajes.</span><span class="sxs-lookup"><span data-stu-id="64bfd-440"> has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="64bfd-441">**Comunicación entre procesos**: un proceso debe hospedar un servidor HTTP/2 para aceptar llamadas gRPC entrantes.</span><span class="sxs-lookup"><span data-stu-id="64bfd-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="64bfd-442">En Windows, las [canalizaciones](/dotnet/standard/io/pipe-operations) de comunicación entre procesos son un método rápido y ligero de comunicación.</span><span class="sxs-lookup"><span data-stu-id="64bfd-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="64bfd-443">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="64bfd-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
