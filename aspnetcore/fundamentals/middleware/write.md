---
<span data-ttu-id="5428d-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5428d-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5428d-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="5428d-102">'Blazor'</span></span>
- <span data-ttu-id="5428d-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="5428d-103">'Identity'</span></span>
- <span data-ttu-id="5428d-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="5428d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5428d-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="5428d-105">'Razor'</span></span>
- <span data-ttu-id="5428d-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5428d-106">'SignalR' uid:</span></span> 

---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="5428d-107">Escritura de middleware de ASP.NET Core personalizado</span><span class="sxs-lookup"><span data-stu-id="5428d-107">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="5428d-108">Por [Rick Anderson](https://twitter.com/RickAndMSFT) y [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5428d-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5428d-109">El software intermedio es un software que se ensambla en una canalización de una aplicación para controlar las solicitudes y las respuestas.</span><span class="sxs-lookup"><span data-stu-id="5428d-109">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="5428d-110">ASP.NET Core proporciona un completo conjunto de componentes de middleware integrados, pero en algunos escenarios es posible que quiera escribir middleware personalizado.</span><span class="sxs-lookup"><span data-stu-id="5428d-110">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="5428d-111">En este tema se describe cómo escribir middleware *basado en convenciones*.</span><span class="sxs-lookup"><span data-stu-id="5428d-111">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="5428d-112">Para ver un enfoque que emplea el establecimiento de tipos seguros y la activación por solicitud, consulte <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="5428d-112">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="5428d-113">Clase de middleware</span><span class="sxs-lookup"><span data-stu-id="5428d-113">Middleware class</span></span>

<span data-ttu-id="5428d-114">El middleware normalmente está encapsulado en una clase y se expone con un método de extensión.</span><span class="sxs-lookup"><span data-stu-id="5428d-114">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="5428d-115">Use el siguiente software intermedio a modo de ejemplo. En este se establece la referencia cultural de la solicitud actual a partir de la cadena de solicitud:</span><span class="sxs-lookup"><span data-stu-id="5428d-115">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="5428d-116">El código de ejemplo anterior se usa para mostrar la creación de un componente de software intermedio.</span><span class="sxs-lookup"><span data-stu-id="5428d-116">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="5428d-117">Para obtener más información sobre la compatibilidad con la localización integrada de ASP.NET Core, vea <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="5428d-117">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="5428d-118">Pruebe el middleware pasando la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="5428d-118">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="5428d-119">Por ejemplo, solicite `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="5428d-119">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="5428d-120">El código siguiente mueve el delegado de middleware a una clase:</span><span class="sxs-lookup"><span data-stu-id="5428d-120">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="5428d-121">La clase de middleware debe incluir:</span><span class="sxs-lookup"><span data-stu-id="5428d-121">The middleware class must include:</span></span>

* <span data-ttu-id="5428d-122">Un constructor público con un parámetro de tipo <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="5428d-122">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="5428d-123">Un método público llamado `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="5428d-123">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="5428d-124">Este método debe:</span><span class="sxs-lookup"><span data-stu-id="5428d-124">This method must:</span></span>
  * <span data-ttu-id="5428d-125">Devolver `Task`.</span><span class="sxs-lookup"><span data-stu-id="5428d-125">Return a `Task`.</span></span>
  * <span data-ttu-id="5428d-126">Aceptar un primer parámetro de tipo <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="5428d-126">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="5428d-127">Los parámetros adicionales para el constructor y `Invoke`/`InvokeAsync` se rellenan mediante la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5428d-127">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="5428d-128">Dependencias de middleware</span><span class="sxs-lookup"><span data-stu-id="5428d-128">Middleware dependencies</span></span>

<span data-ttu-id="5428d-129">El middleware debería seguir el [principio de dependencias explicitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) mediante la exposición de sus dependencias en el constructor.</span><span class="sxs-lookup"><span data-stu-id="5428d-129">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="5428d-130">El middleware se construye una vez por *duración de la aplicación*.</span><span class="sxs-lookup"><span data-stu-id="5428d-130">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="5428d-131">Si necesita compartir servicios con software intermedio en una solicitud, vea la sección [Dependencias de middleware bajo solicitud](#per-request-middleware-dependencies).</span><span class="sxs-lookup"><span data-stu-id="5428d-131">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="5428d-132">Los componentes de software intermedio pueden resolver sus dependencias de una [inserción de dependencias (DI)](xref:fundamentals/dependency-injection) mediante parámetros del constructor.</span><span class="sxs-lookup"><span data-stu-id="5428d-132">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="5428d-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) también puede aceptar parámetros adicionales directamente.</span><span class="sxs-lookup"><span data-stu-id="5428d-133">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="5428d-134">Dependencias de middleware bajo solicitud</span><span class="sxs-lookup"><span data-stu-id="5428d-134">Per-request middleware dependencies</span></span>

<span data-ttu-id="5428d-135">Dado que el software intermedio se construye al inicio de la aplicación y no bajo solicitud, los servicios de duración *con ámbito* que usan los constructores de software intermedio no se comparten con otros tipos insertados mediante dependencias durante cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="5428d-135">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="5428d-136">Si debe compartir un servicio *con ámbito* entre su middleware y otros tipos, agregue esos servicios a la signatura del método `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="5428d-136">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="5428d-137">El método `Invoke` puede aceptar parámetros adicionales que la inserción de dependencias propaga:</span><span class="sxs-lookup"><span data-stu-id="5428d-137">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="5428d-138">Método de extensión de middleware</span><span class="sxs-lookup"><span data-stu-id="5428d-138">Middleware extension method</span></span>

<span data-ttu-id="5428d-139">El método de extensión siguiente expone el software intermedio mediante <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="5428d-139">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="5428d-140">El código siguiente llama al middleware desde `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5428d-140">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="5428d-141">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="5428d-141">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
