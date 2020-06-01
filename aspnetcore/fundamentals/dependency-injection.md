---
<span data-ttu-id="db7b5-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-102">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-102">'Blazor'</span></span>
- <span data-ttu-id="db7b5-103">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-103">'Identity'</span></span>
- <span data-ttu-id="db7b5-104">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-105">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-105">'Razor'</span></span>
- <span data-ttu-id="db7b5-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="db7b5-107">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db7b5-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="db7b5-108">Por [Steve Smith](https://ardalis.com/) y [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="db7b5-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db7b5-109">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="db7b5-110">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="db7b5-111">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db7b5-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="db7b5-112">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="db7b5-112">Overview of dependency injection</span></span>

<span data-ttu-id="db7b5-113">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="db7b5-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="db7b5-114">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="db7b5-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="db7b5-115">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="db7b5-116">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="db7b5-117">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="db7b5-118">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="db7b5-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="db7b5-119">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="db7b5-120">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="db7b5-121">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="db7b5-122">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="db7b5-123">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="db7b5-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="db7b5-124">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="db7b5-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="db7b5-125">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="db7b5-126">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="db7b5-127">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="db7b5-128">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="db7b5-129">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="db7b5-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="db7b5-130">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="db7b5-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="db7b5-131">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="db7b5-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="db7b5-132">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="db7b5-133">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="db7b5-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="db7b5-134">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="db7b5-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="db7b5-135">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="db7b5-136">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="db7b5-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="db7b5-137">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="db7b5-138">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="db7b5-139">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="db7b5-140">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="db7b5-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="db7b5-141">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="db7b5-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="db7b5-142">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="db7b5-143">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="db7b5-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="db7b5-144">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="db7b5-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="db7b5-145">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="db7b5-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="db7b5-146">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="db7b5-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="db7b5-147">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="db7b5-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="db7b5-148">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="db7b5-149">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="db7b5-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="db7b5-150">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="db7b5-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="db7b5-151">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="db7b5-152">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="db7b5-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="db7b5-153">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="db7b5-153">Services injected into Startup</span></span>

<span data-ttu-id="db7b5-154">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="db7b5-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="db7b5-155">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="db7b5-156">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="db7b5-157">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="db7b5-157">Framework-provided services</span></span>

<span data-ttu-id="db7b5-158">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="db7b5-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="db7b5-159">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="db7b5-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="db7b5-160">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="db7b5-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="db7b5-161">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="db7b5-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="db7b5-162">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="db7b5-162">Service Type</span></span> | <span data-ttu-id="db7b5-163">Período de duración</span><span class="sxs-lookup"><span data-stu-id="db7b5-163">Lifetime</span></span> |
| ---
<span data-ttu-id="db7b5-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-165">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-165">'Blazor'</span></span>
- <span data-ttu-id="db7b5-166">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-166">'Identity'</span></span>
- <span data-ttu-id="db7b5-167">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-168">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-168">'Razor'</span></span>
- <span data-ttu-id="db7b5-169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-171">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-171">'Blazor'</span></span>
- <span data-ttu-id="db7b5-172">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-172">'Identity'</span></span>
- <span data-ttu-id="db7b5-173">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-174">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-174">'Razor'</span></span>
- <span data-ttu-id="db7b5-175">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-177">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-177">'Blazor'</span></span>
- <span data-ttu-id="db7b5-178">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-178">'Identity'</span></span>
- <span data-ttu-id="db7b5-179">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-180">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-180">'Razor'</span></span>
- <span data-ttu-id="db7b5-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-183">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-183">'Blazor'</span></span>
- <span data-ttu-id="db7b5-184">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-184">'Identity'</span></span>
- <span data-ttu-id="db7b5-185">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-186">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-186">'Razor'</span></span>
- <span data-ttu-id="db7b5-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-187">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-189">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-189">'Blazor'</span></span>
- <span data-ttu-id="db7b5-190">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-190">'Identity'</span></span>
- <span data-ttu-id="db7b5-191">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-192">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-192">'Razor'</span></span>
- <span data-ttu-id="db7b5-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-195">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-195">'Blazor'</span></span>
- <span data-ttu-id="db7b5-196">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-196">'Identity'</span></span>
- <span data-ttu-id="db7b5-197">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-198">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-198">'Razor'</span></span>
- <span data-ttu-id="db7b5-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-199">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitorio | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitorio | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitorio | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitorio | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="db7b5-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="db7b5-201">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="db7b5-201">Register additional services with extension methods</span></span>

<span data-ttu-id="db7b5-202">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="db7b5-203">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="db7b5-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="db7b5-204">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="db7b5-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="db7b5-205">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="db7b5-205">Service lifetimes</span></span>

<span data-ttu-id="db7b5-206">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="db7b5-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="db7b5-207">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="db7b5-208">Transitorio</span><span class="sxs-lookup"><span data-stu-id="db7b5-208">Transient</span></span>

<span data-ttu-id="db7b5-209">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="db7b5-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="db7b5-210">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="db7b5-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="db7b5-211">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="db7b5-211">Scoped</span></span>

<span data-ttu-id="db7b5-212">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="db7b5-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="db7b5-213">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="db7b5-214">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="db7b5-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="db7b5-215">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="db7b5-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="db7b5-216">Singleton</span></span>

<span data-ttu-id="db7b5-217">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="db7b5-218">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="db7b5-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="db7b5-219">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="db7b5-220">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="db7b5-221">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="db7b5-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="db7b5-222">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="db7b5-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="db7b5-223">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="db7b5-223">Service registration methods</span></span>

<span data-ttu-id="db7b5-224">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="db7b5-225">Método</span><span class="sxs-lookup"><span data-stu-id="db7b5-225">Method</span></span> | <span data-ttu-id="db7b5-226">Automático</span><span class="sxs-lookup"><span data-stu-id="db7b5-226">Automatic</span></span><br><span data-ttu-id="db7b5-227">objeto</span><span class="sxs-lookup"><span data-stu-id="db7b5-227">object</span></span><br><span data-ttu-id="db7b5-228">eliminación</span><span class="sxs-lookup"><span data-stu-id="db7b5-228">disposal</span></span> | <span data-ttu-id="db7b5-229">Múltiple</span><span class="sxs-lookup"><span data-stu-id="db7b5-229">Multiple</span></span><br><span data-ttu-id="db7b5-230">implementaciones</span><span class="sxs-lookup"><span data-stu-id="db7b5-230">implementations</span></span> | <span data-ttu-id="db7b5-231">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="db7b5-231">Pass args</span></span> |
| ---
<span data-ttu-id="db7b5-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-233">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-233">'Blazor'</span></span>
- <span data-ttu-id="db7b5-234">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-234">'Identity'</span></span>
- <span data-ttu-id="db7b5-235">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-236">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-236">'Razor'</span></span>
- <span data-ttu-id="db7b5-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-237">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-239">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-239">'Blazor'</span></span>
- <span data-ttu-id="db7b5-240">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-240">'Identity'</span></span>
- <span data-ttu-id="db7b5-241">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-242">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-242">'Razor'</span></span>
- <span data-ttu-id="db7b5-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-245">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-245">'Blazor'</span></span>
- <span data-ttu-id="db7b5-246">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-246">'Identity'</span></span>
- <span data-ttu-id="db7b5-247">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-248">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-248">'Razor'</span></span>
- <span data-ttu-id="db7b5-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-251">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-251">'Blazor'</span></span>
- <span data-ttu-id="db7b5-252">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-252">'Identity'</span></span>
- <span data-ttu-id="db7b5-253">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-254">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-254">'Razor'</span></span>
- <span data-ttu-id="db7b5-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-257">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-257">'Blazor'</span></span>
- <span data-ttu-id="db7b5-258">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-258">'Identity'</span></span>
- <span data-ttu-id="db7b5-259">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-260">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-260">'Razor'</span></span>
- <span data-ttu-id="db7b5-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-263">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-263">'Blazor'</span></span>
- <span data-ttu-id="db7b5-264">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-264">'Identity'</span></span>
- <span data-ttu-id="db7b5-265">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-266">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-266">'Razor'</span></span>
- <span data-ttu-id="db7b5-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-269">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-269">'Blazor'</span></span>
- <span data-ttu-id="db7b5-270">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-270">'Identity'</span></span>
- <span data-ttu-id="db7b5-271">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-272">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-272">'Razor'</span></span>
- <span data-ttu-id="db7b5-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-275">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-275">'Blazor'</span></span>
- <span data-ttu-id="db7b5-276">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-276">'Identity'</span></span>
- <span data-ttu-id="db7b5-277">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-278">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-278">'Razor'</span></span>
- <span data-ttu-id="db7b5-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-281">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-281">'Blazor'</span></span>
- <span data-ttu-id="db7b5-282">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-282">'Identity'</span></span>
- <span data-ttu-id="db7b5-283">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-284">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-284">'Razor'</span></span>
- <span data-ttu-id="db7b5-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-287">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-287">'Blazor'</span></span>
- <span data-ttu-id="db7b5-288">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-288">'Identity'</span></span>
- <span data-ttu-id="db7b5-289">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-290">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-290">'Razor'</span></span>
- <span data-ttu-id="db7b5-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-293">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-293">'Blazor'</span></span>
- <span data-ttu-id="db7b5-294">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-294">'Identity'</span></span>
- <span data-ttu-id="db7b5-295">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-296">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-296">'Razor'</span></span>
- <span data-ttu-id="db7b5-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-299">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-299">'Blazor'</span></span>
- <span data-ttu-id="db7b5-300">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-300">'Identity'</span></span>
- <span data-ttu-id="db7b5-301">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-302">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-302">'Razor'</span></span>
- <span data-ttu-id="db7b5-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-305">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-305">'Blazor'</span></span>
- <span data-ttu-id="db7b5-306">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-306">'Identity'</span></span>
- <span data-ttu-id="db7b5-307">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-308">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-308">'Razor'</span></span>
- <span data-ttu-id="db7b5-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-309">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-311">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-311">'Blazor'</span></span>
- <span data-ttu-id="db7b5-312">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-312">'Identity'</span></span>
- <span data-ttu-id="db7b5-313">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-314">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-314">'Razor'</span></span>
- <span data-ttu-id="db7b5-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-317">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-317">'Blazor'</span></span>
- <span data-ttu-id="db7b5-318">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-318">'Identity'</span></span>
- <span data-ttu-id="db7b5-319">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-320">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-320">'Razor'</span></span>
- <span data-ttu-id="db7b5-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-323">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-323">'Blazor'</span></span>
- <span data-ttu-id="db7b5-324">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-324">'Identity'</span></span>
- <span data-ttu-id="db7b5-325">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-326">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-326">'Razor'</span></span>
- <span data-ttu-id="db7b5-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-329">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-329">'Blazor'</span></span>
- <span data-ttu-id="db7b5-330">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-330">'Identity'</span></span>
- <span data-ttu-id="db7b5-331">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-332">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-332">'Razor'</span></span>
- <span data-ttu-id="db7b5-333">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-335">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-335">'Blazor'</span></span>
- <span data-ttu-id="db7b5-336">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-336">'Identity'</span></span>
- <span data-ttu-id="db7b5-337">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-338">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-338">'Razor'</span></span>
- <span data-ttu-id="db7b5-339">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-341">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-341">'Blazor'</span></span>
- <span data-ttu-id="db7b5-342">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-342">'Identity'</span></span>
- <span data-ttu-id="db7b5-343">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-344">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-344">'Razor'</span></span>
- <span data-ttu-id="db7b5-345">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-347">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-347">'Blazor'</span></span>
- <span data-ttu-id="db7b5-348">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-348">'Identity'</span></span>
- <span data-ttu-id="db7b5-349">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-350">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-350">'Razor'</span></span>
- <span data-ttu-id="db7b5-351">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-353">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-353">'Blazor'</span></span>
- <span data-ttu-id="db7b5-354">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-354">'Identity'</span></span>
- <span data-ttu-id="db7b5-355">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-356">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-356">'Razor'</span></span>
- <span data-ttu-id="db7b5-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-359">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-359">'Blazor'</span></span>
- <span data-ttu-id="db7b5-360">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-360">'Identity'</span></span>
- <span data-ttu-id="db7b5-361">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-362">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-362">'Razor'</span></span>
- <span data-ttu-id="db7b5-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-365">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-365">'Blazor'</span></span>
- <span data-ttu-id="db7b5-366">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-366">'Identity'</span></span>
- <span data-ttu-id="db7b5-367">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-368">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-368">'Razor'</span></span>
- <span data-ttu-id="db7b5-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-369">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-371">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-371">'Blazor'</span></span>
- <span data-ttu-id="db7b5-372">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-372">'Identity'</span></span>
- <span data-ttu-id="db7b5-373">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-374">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-374">'Razor'</span></span>
- <span data-ttu-id="db7b5-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-375">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="db7b5-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="db7b5-377">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db7b5-377">Example:</span></span><br><span data-ttu-id="db7b5-378">`services.AddSingleton<IMyDep, MyDep>();` | Sí | Sí | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="db7b5-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="db7b5-379">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="db7b5-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Sí | Sí | Sí | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="db7b5-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="db7b5-381">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db7b5-381">Example:</span></span><br><span data-ttu-id="db7b5-382">`services.AddSingleton<MyDep>();` | Sí | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="db7b5-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="db7b5-383">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="db7b5-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Sí | Sí | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="db7b5-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="db7b5-385">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="db7b5-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Sí |</span><span class="sxs-lookup"><span data-stu-id="db7b5-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="db7b5-387">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="db7b5-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="db7b5-388">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="db7b5-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="db7b5-389">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="db7b5-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="db7b5-390">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="db7b5-391">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="db7b5-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="db7b5-392">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="db7b5-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="db7b5-393">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="db7b5-394">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="db7b5-395">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="db7b5-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="db7b5-396">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="db7b5-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="db7b5-397">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="db7b5-398">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="db7b5-399">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="db7b5-400">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="db7b5-400">Constructor injection behavior</span></span>

<span data-ttu-id="db7b5-401">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="db7b5-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="db7b5-403">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="db7b5-404">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="db7b5-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="db7b5-405">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="db7b5-406">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="db7b5-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="db7b5-407">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="db7b5-408">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="db7b5-408">Entity Framework contexts</span></span>

<span data-ttu-id="db7b5-409">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="db7b5-410">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="db7b5-411">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="db7b5-412">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="db7b5-412">Lifetime and registration options</span></span>

<span data-ttu-id="db7b5-413">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="db7b5-414">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="db7b5-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="db7b5-415">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="db7b5-416">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="db7b5-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="db7b5-417">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="db7b5-418">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="db7b5-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="db7b5-419">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="db7b5-420">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="db7b5-421">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="db7b5-422">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="db7b5-423">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="db7b5-424">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="db7b5-425">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="db7b5-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="db7b5-426">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="db7b5-427">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="db7b5-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="db7b5-428">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="db7b5-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="db7b5-429">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="db7b5-430">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="db7b5-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="db7b5-431">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="db7b5-432">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="db7b5-432">**First request:**</span></span>

<span data-ttu-id="db7b5-433">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="db7b5-433">Controller operations:</span></span>

<span data-ttu-id="db7b5-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="db7b5-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="db7b5-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="db7b5-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="db7b5-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-437">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-438">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-438">`OperationService` operations:</span></span>

<span data-ttu-id="db7b5-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="db7b5-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="db7b5-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="db7b5-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="db7b5-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-442">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-443">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="db7b5-443">**Second request:**</span></span>

<span data-ttu-id="db7b5-444">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="db7b5-444">Controller operations:</span></span>

<span data-ttu-id="db7b5-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="db7b5-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="db7b5-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="db7b5-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="db7b5-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-448">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-449">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-449">`OperationService` operations:</span></span>

<span data-ttu-id="db7b5-450">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="db7b5-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="db7b5-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="db7b5-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="db7b5-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-453">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-454">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="db7b5-455">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="db7b5-455">*Transient* objects are always different.</span></span> <span data-ttu-id="db7b5-456">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="db7b5-457">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="db7b5-458">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="db7b5-459">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="db7b5-460">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="db7b5-460">Call services from main</span></span>

<span data-ttu-id="db7b5-461">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="db7b5-462">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="db7b5-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="db7b5-463">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a><span data-ttu-id="db7b5-464">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="db7b5-464">Scope validation</span></span>

<span data-ttu-id="db7b5-465">Cuando la aplicación se ejecuta en el entorno de desarrollo y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="db7b5-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="db7b5-466">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="db7b5-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="db7b5-467">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="db7b5-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="db7b5-468">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="db7b5-469">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="db7b5-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="db7b5-470">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="db7b5-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="db7b5-471">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="db7b5-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="db7b5-472">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="db7b5-473">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="db7b5-474">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="db7b5-474">Request Services</span></span>

<span data-ttu-id="db7b5-475">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="db7b5-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="db7b5-476">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="db7b5-477">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="db7b5-478">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="db7b5-479">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="db7b5-480">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="db7b5-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="db7b5-481">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="db7b5-482">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="db7b5-482">Design services for dependency injection</span></span>

<span data-ttu-id="db7b5-483">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="db7b5-483">Best practices are to:</span></span>

* <span data-ttu-id="db7b5-484">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="db7b5-485">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="db7b5-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="db7b5-486">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="db7b5-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="db7b5-487">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="db7b5-488">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="db7b5-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="db7b5-489">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="db7b5-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="db7b5-490">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="db7b5-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="db7b5-491">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="db7b5-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="db7b5-492">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="db7b5-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="db7b5-493">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="db7b5-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="db7b5-494">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="db7b5-494">Disposal of services</span></span>

<span data-ttu-id="db7b5-495">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="db7b5-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="db7b5-496">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="db7b5-497">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="db7b5-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="db7b5-498">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="db7b5-498">In the following example:</span></span>

* <span data-ttu-id="db7b5-499">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="db7b5-500">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="db7b5-501">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="db7b5-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="db7b5-502">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="db7b5-503">Para analizar las opciones de eliminación de los servicios, consulte [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/) (Cuatro formas de eliminar IDisposables en ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="db7b5-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="db7b5-504">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="db7b5-504">Default service container replacement</span></span>

<span data-ttu-id="db7b5-505">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="db7b5-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="db7b5-506">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="db7b5-507">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="db7b5-507">Property injection</span></span>
* <span data-ttu-id="db7b5-508">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="db7b5-508">Injection based on name</span></span>
* <span data-ttu-id="db7b5-509">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="db7b5-509">Child containers</span></span>
* <span data-ttu-id="db7b5-510">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="db7b5-510">Custom lifetime management</span></span>
* <span data-ttu-id="db7b5-511">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="db7b5-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="db7b5-512">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="db7b5-512">Convention-based registration</span></span>

<span data-ttu-id="db7b5-513">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="db7b5-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="db7b5-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="db7b5-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="db7b5-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="db7b5-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="db7b5-516">Grace</span><span class="sxs-lookup"><span data-stu-id="db7b5-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="db7b5-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="db7b5-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="db7b5-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="db7b5-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="db7b5-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="db7b5-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="db7b5-520">Unity</span><span class="sxs-lookup"><span data-stu-id="db7b5-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="db7b5-521">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="db7b5-521">Thread safety</span></span>

<span data-ttu-id="db7b5-522">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="db7b5-523">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="db7b5-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="db7b5-524">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="db7b5-525">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="db7b5-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="db7b5-526">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="db7b5-526">Recommendations</span></span>

* <span data-ttu-id="db7b5-527">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="db7b5-528">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="db7b5-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="db7b5-529">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="db7b5-530">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="db7b5-531">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="db7b5-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="db7b5-532">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="db7b5-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="db7b5-533">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="db7b5-534">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="db7b5-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="db7b5-535">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="db7b5-536">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="db7b5-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="db7b5-537">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="db7b5-537">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="db7b5-538">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="db7b5-538">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="db7b5-539">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="db7b5-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="db7b5-540">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="db7b5-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="db7b5-541">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="db7b5-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="db7b5-542">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="db7b5-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="db7b5-543">Las excepciones son poco frecuentes; principalmente en casos especiales dentro del propio marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="db7b5-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="db7b5-544">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="db7b5-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="db7b5-545">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="db7b5-546">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="db7b5-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="db7b5-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) proporciona servicios multiinquilino.</span><span class="sxs-lookup"><span data-stu-id="db7b5-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="db7b5-548">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="db7b5-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="db7b5-549">Vea las aplicaciones de ejemplo en https://github.com/OrchardCMS/OrchardCore.Samples para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="db7b5-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db7b5-550">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="db7b5-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="db7b5-551">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="db7b5-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="db7b5-552">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="db7b5-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="db7b5-553">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="db7b5-553">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="db7b5-554">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db7b5-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="db7b5-555">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="db7b5-556">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="db7b5-557">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db7b5-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="db7b5-558">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="db7b5-558">Overview of dependency injection</span></span>

<span data-ttu-id="db7b5-559">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="db7b5-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="db7b5-560">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="db7b5-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="db7b5-561">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="db7b5-562">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="db7b5-563">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="db7b5-564">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="db7b5-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="db7b5-565">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="db7b5-566">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="db7b5-567">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="db7b5-568">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="db7b5-569">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="db7b5-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="db7b5-570">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="db7b5-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="db7b5-571">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="db7b5-572">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="db7b5-573">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="db7b5-574">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="db7b5-575">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="db7b5-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="db7b5-576">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="db7b5-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="db7b5-577">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="db7b5-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="db7b5-578">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="db7b5-579">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="db7b5-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="db7b5-580">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="db7b5-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="db7b5-581">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="db7b5-582">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="db7b5-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="db7b5-583">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="db7b5-584">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="db7b5-585">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="db7b5-586">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="db7b5-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="db7b5-587">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="db7b5-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="db7b5-588">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="db7b5-589">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="db7b5-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="db7b5-590">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="db7b5-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="db7b5-591">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="db7b5-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="db7b5-592">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="db7b5-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="db7b5-593">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="db7b5-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="db7b5-594">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="db7b5-595">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="db7b5-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="db7b5-596">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="db7b5-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="db7b5-597">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="db7b5-598">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="db7b5-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="db7b5-599">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="db7b5-599">Services injected into Startup</span></span>

<span data-ttu-id="db7b5-600">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="db7b5-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="db7b5-601">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="db7b5-602">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="db7b5-603">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="db7b5-603">Framework-provided services</span></span>

<span data-ttu-id="db7b5-604">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="db7b5-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="db7b5-605">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="db7b5-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="db7b5-606">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="db7b5-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="db7b5-607">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="db7b5-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="db7b5-608">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="db7b5-608">Service Type</span></span> | <span data-ttu-id="db7b5-609">Período de duración</span><span class="sxs-lookup"><span data-stu-id="db7b5-609">Lifetime</span></span> |
| ---
<span data-ttu-id="db7b5-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-611">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-611">'Blazor'</span></span>
- <span data-ttu-id="db7b5-612">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-612">'Identity'</span></span>
- <span data-ttu-id="db7b5-613">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-614">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-614">'Razor'</span></span>
- <span data-ttu-id="db7b5-615">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-617">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-617">'Blazor'</span></span>
- <span data-ttu-id="db7b5-618">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-618">'Identity'</span></span>
- <span data-ttu-id="db7b5-619">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-620">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-620">'Razor'</span></span>
- <span data-ttu-id="db7b5-621">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-623">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-623">'Blazor'</span></span>
- <span data-ttu-id="db7b5-624">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-624">'Identity'</span></span>
- <span data-ttu-id="db7b5-625">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-626">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-626">'Razor'</span></span>
- <span data-ttu-id="db7b5-627">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-629">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-629">'Blazor'</span></span>
- <span data-ttu-id="db7b5-630">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-630">'Identity'</span></span>
- <span data-ttu-id="db7b5-631">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-632">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-632">'Razor'</span></span>
- <span data-ttu-id="db7b5-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-633">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-635">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-635">'Blazor'</span></span>
- <span data-ttu-id="db7b5-636">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-636">'Identity'</span></span>
- <span data-ttu-id="db7b5-637">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-638">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-638">'Razor'</span></span>
- <span data-ttu-id="db7b5-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-641">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-641">'Blazor'</span></span>
- <span data-ttu-id="db7b5-642">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-642">'Identity'</span></span>
- <span data-ttu-id="db7b5-643">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-644">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-644">'Razor'</span></span>
- <span data-ttu-id="db7b5-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-645">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transitorio | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transitorio | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transitorio | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transitorio | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="db7b5-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="db7b5-647">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="db7b5-647">Register additional services with extension methods</span></span>

<span data-ttu-id="db7b5-648">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="db7b5-649">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="db7b5-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="db7b5-650">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="db7b5-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="db7b5-651">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="db7b5-651">Service lifetimes</span></span>

<span data-ttu-id="db7b5-652">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="db7b5-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="db7b5-653">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="db7b5-654">Transitorio</span><span class="sxs-lookup"><span data-stu-id="db7b5-654">Transient</span></span>

<span data-ttu-id="db7b5-655">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="db7b5-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="db7b5-656">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="db7b5-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="db7b5-657">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="db7b5-657">Scoped</span></span>

<span data-ttu-id="db7b5-658">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="db7b5-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="db7b5-659">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="db7b5-660">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="db7b5-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="db7b5-661">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="db7b5-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="db7b5-662">Singleton</span></span>

<span data-ttu-id="db7b5-663">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="db7b5-664">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="db7b5-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="db7b5-665">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="db7b5-666">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="db7b5-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="db7b5-667">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="db7b5-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="db7b5-668">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="db7b5-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="db7b5-669">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="db7b5-669">Service registration methods</span></span>

<span data-ttu-id="db7b5-670">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="db7b5-671">Método</span><span class="sxs-lookup"><span data-stu-id="db7b5-671">Method</span></span> | <span data-ttu-id="db7b5-672">Automático</span><span class="sxs-lookup"><span data-stu-id="db7b5-672">Automatic</span></span><br><span data-ttu-id="db7b5-673">objeto</span><span class="sxs-lookup"><span data-stu-id="db7b5-673">object</span></span><br><span data-ttu-id="db7b5-674">eliminación</span><span class="sxs-lookup"><span data-stu-id="db7b5-674">disposal</span></span> | <span data-ttu-id="db7b5-675">Múltiple</span><span class="sxs-lookup"><span data-stu-id="db7b5-675">Multiple</span></span><br><span data-ttu-id="db7b5-676">implementaciones</span><span class="sxs-lookup"><span data-stu-id="db7b5-676">implementations</span></span> | <span data-ttu-id="db7b5-677">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="db7b5-677">Pass args</span></span> |
| ---
<span data-ttu-id="db7b5-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-679">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-679">'Blazor'</span></span>
- <span data-ttu-id="db7b5-680">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-680">'Identity'</span></span>
- <span data-ttu-id="db7b5-681">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-682">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-682">'Razor'</span></span>
- <span data-ttu-id="db7b5-683">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-683">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-685">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-685">'Blazor'</span></span>
- <span data-ttu-id="db7b5-686">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-686">'Identity'</span></span>
- <span data-ttu-id="db7b5-687">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-688">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-688">'Razor'</span></span>
- <span data-ttu-id="db7b5-689">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-691">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-691">'Blazor'</span></span>
- <span data-ttu-id="db7b5-692">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-692">'Identity'</span></span>
- <span data-ttu-id="db7b5-693">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-694">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-694">'Razor'</span></span>
- <span data-ttu-id="db7b5-695">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-697">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-697">'Blazor'</span></span>
- <span data-ttu-id="db7b5-698">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-698">'Identity'</span></span>
- <span data-ttu-id="db7b5-699">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-700">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-700">'Razor'</span></span>
- <span data-ttu-id="db7b5-701">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-703">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-703">'Blazor'</span></span>
- <span data-ttu-id="db7b5-704">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-704">'Identity'</span></span>
- <span data-ttu-id="db7b5-705">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-706">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-706">'Razor'</span></span>
- <span data-ttu-id="db7b5-707">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-709">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-709">'Blazor'</span></span>
- <span data-ttu-id="db7b5-710">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-710">'Identity'</span></span>
- <span data-ttu-id="db7b5-711">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-712">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-712">'Razor'</span></span>
- <span data-ttu-id="db7b5-713">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-715">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-715">'Blazor'</span></span>
- <span data-ttu-id="db7b5-716">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-716">'Identity'</span></span>
- <span data-ttu-id="db7b5-717">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-718">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-718">'Razor'</span></span>
- <span data-ttu-id="db7b5-719">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-721">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-721">'Blazor'</span></span>
- <span data-ttu-id="db7b5-722">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-722">'Identity'</span></span>
- <span data-ttu-id="db7b5-723">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-724">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-724">'Razor'</span></span>
- <span data-ttu-id="db7b5-725">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-727">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-727">'Blazor'</span></span>
- <span data-ttu-id="db7b5-728">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-728">'Identity'</span></span>
- <span data-ttu-id="db7b5-729">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-730">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-730">'Razor'</span></span>
- <span data-ttu-id="db7b5-731">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-733">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-733">'Blazor'</span></span>
- <span data-ttu-id="db7b5-734">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-734">'Identity'</span></span>
- <span data-ttu-id="db7b5-735">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-736">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-736">'Razor'</span></span>
- <span data-ttu-id="db7b5-737">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-739">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-739">'Blazor'</span></span>
- <span data-ttu-id="db7b5-740">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-740">'Identity'</span></span>
- <span data-ttu-id="db7b5-741">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-742">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-742">'Razor'</span></span>
- <span data-ttu-id="db7b5-743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-745">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-745">'Blazor'</span></span>
- <span data-ttu-id="db7b5-746">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-746">'Identity'</span></span>
- <span data-ttu-id="db7b5-747">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-748">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-748">'Razor'</span></span>
- <span data-ttu-id="db7b5-749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-751">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-751">'Blazor'</span></span>
- <span data-ttu-id="db7b5-752">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-752">'Identity'</span></span>
- <span data-ttu-id="db7b5-753">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-754">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-754">'Razor'</span></span>
- <span data-ttu-id="db7b5-755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-755">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-757">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-757">'Blazor'</span></span>
- <span data-ttu-id="db7b5-758">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-758">'Identity'</span></span>
- <span data-ttu-id="db7b5-759">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-760">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-760">'Razor'</span></span>
- <span data-ttu-id="db7b5-761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-763">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-763">'Blazor'</span></span>
- <span data-ttu-id="db7b5-764">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-764">'Identity'</span></span>
- <span data-ttu-id="db7b5-765">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-766">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-766">'Razor'</span></span>
- <span data-ttu-id="db7b5-767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-769">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-769">'Blazor'</span></span>
- <span data-ttu-id="db7b5-770">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-770">'Identity'</span></span>
- <span data-ttu-id="db7b5-771">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-772">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-772">'Razor'</span></span>
- <span data-ttu-id="db7b5-773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-775">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-775">'Blazor'</span></span>
- <span data-ttu-id="db7b5-776">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-776">'Identity'</span></span>
- <span data-ttu-id="db7b5-777">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-778">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-778">'Razor'</span></span>
- <span data-ttu-id="db7b5-779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-781">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-781">'Blazor'</span></span>
- <span data-ttu-id="db7b5-782">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-782">'Identity'</span></span>
- <span data-ttu-id="db7b5-783">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-784">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-784">'Razor'</span></span>
- <span data-ttu-id="db7b5-785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-787">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-787">'Blazor'</span></span>
- <span data-ttu-id="db7b5-788">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-788">'Identity'</span></span>
- <span data-ttu-id="db7b5-789">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-790">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-790">'Razor'</span></span>
- <span data-ttu-id="db7b5-791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-793">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-793">'Blazor'</span></span>
- <span data-ttu-id="db7b5-794">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-794">'Identity'</span></span>
- <span data-ttu-id="db7b5-795">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-796">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-796">'Razor'</span></span>
- <span data-ttu-id="db7b5-797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-799">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-799">'Blazor'</span></span>
- <span data-ttu-id="db7b5-800">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-800">'Identity'</span></span>
- <span data-ttu-id="db7b5-801">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-802">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-802">'Razor'</span></span>
- <span data-ttu-id="db7b5-803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-805">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-805">'Blazor'</span></span>
- <span data-ttu-id="db7b5-806">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-806">'Identity'</span></span>
- <span data-ttu-id="db7b5-807">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-808">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-808">'Razor'</span></span>
- <span data-ttu-id="db7b5-809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="db7b5-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-811">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-811">'Blazor'</span></span>
- <span data-ttu-id="db7b5-812">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-812">'Identity'</span></span>
- <span data-ttu-id="db7b5-813">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-814">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-814">'Razor'</span></span>
- <span data-ttu-id="db7b5-815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-815">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="db7b5-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="db7b5-817">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-817">'Blazor'</span></span>
- <span data-ttu-id="db7b5-818">"Identity"</span><span class="sxs-lookup"><span data-stu-id="db7b5-818">'Identity'</span></span>
- <span data-ttu-id="db7b5-819">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="db7b5-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="db7b5-820">"Razor"</span><span class="sxs-lookup"><span data-stu-id="db7b5-820">'Razor'</span></span>
- <span data-ttu-id="db7b5-821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="db7b5-821">'SignalR' uid:</span></span> 

<span data-ttu-id="db7b5-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="db7b5-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="db7b5-823">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db7b5-823">Example:</span></span><br><span data-ttu-id="db7b5-824">`services.AddSingleton<IMyDep, MyDep>();` | Sí | Sí | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="db7b5-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="db7b5-825">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="db7b5-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Sí | Sí | Sí | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="db7b5-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="db7b5-827">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="db7b5-827">Example:</span></span><br><span data-ttu-id="db7b5-828">`services.AddSingleton<MyDep>();` | Sí | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="db7b5-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="db7b5-829">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="db7b5-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Sí | Sí | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="db7b5-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="db7b5-831">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="db7b5-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Sí |</span><span class="sxs-lookup"><span data-stu-id="db7b5-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="db7b5-833">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="db7b5-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="db7b5-834">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="db7b5-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="db7b5-835">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="db7b5-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="db7b5-836">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="db7b5-837">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="db7b5-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="db7b5-838">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="db7b5-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="db7b5-839">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="db7b5-840">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="db7b5-841">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="db7b5-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="db7b5-842">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="db7b5-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="db7b5-843">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="db7b5-844">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="db7b5-845">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="db7b5-846">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="db7b5-846">Constructor injection behavior</span></span>

<span data-ttu-id="db7b5-847">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="db7b5-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="db7b5-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="db7b5-849">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="db7b5-850">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="db7b5-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="db7b5-851">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="db7b5-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="db7b5-852">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="db7b5-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="db7b5-853">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="db7b5-854">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="db7b5-854">Entity Framework contexts</span></span>

<span data-ttu-id="db7b5-855">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="db7b5-856">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="db7b5-857">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="db7b5-858">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="db7b5-858">Lifetime and registration options</span></span>

<span data-ttu-id="db7b5-859">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="db7b5-860">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="db7b5-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="db7b5-861">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="db7b5-862">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="db7b5-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="db7b5-863">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="db7b5-864">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="db7b5-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="db7b5-865">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="db7b5-866">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="db7b5-867">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="db7b5-868">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="db7b5-869">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="db7b5-870">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="db7b5-871">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="db7b5-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="db7b5-872">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="db7b5-873">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="db7b5-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="db7b5-874">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="db7b5-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="db7b5-875">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="db7b5-876">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="db7b5-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="db7b5-877">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="db7b5-878">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="db7b5-878">**First request:**</span></span>

<span data-ttu-id="db7b5-879">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="db7b5-879">Controller operations:</span></span>

<span data-ttu-id="db7b5-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="db7b5-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="db7b5-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="db7b5-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="db7b5-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-883">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-884">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-884">`OperationService` operations:</span></span>

<span data-ttu-id="db7b5-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="db7b5-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="db7b5-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="db7b5-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="db7b5-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-888">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-889">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="db7b5-889">**Second request:**</span></span>

<span data-ttu-id="db7b5-890">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="db7b5-890">Controller operations:</span></span>

<span data-ttu-id="db7b5-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="db7b5-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="db7b5-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="db7b5-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="db7b5-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-894">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-895">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-895">`OperationService` operations:</span></span>

<span data-ttu-id="db7b5-896">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="db7b5-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="db7b5-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="db7b5-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="db7b5-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="db7b5-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="db7b5-899">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="db7b5-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="db7b5-900">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="db7b5-901">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="db7b5-901">*Transient* objects are always different.</span></span> <span data-ttu-id="db7b5-902">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="db7b5-903">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="db7b5-904">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="db7b5-905">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="db7b5-906">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="db7b5-906">Call services from main</span></span>

<span data-ttu-id="db7b5-907">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="db7b5-908">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="db7b5-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="db7b5-909">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="db7b5-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="db7b5-910">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="db7b5-910">Scope validation</span></span>

<span data-ttu-id="db7b5-911">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="db7b5-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="db7b5-912">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="db7b5-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="db7b5-913">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="db7b5-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="db7b5-914">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="db7b5-915">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="db7b5-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="db7b5-916">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="db7b5-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="db7b5-917">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="db7b5-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="db7b5-918">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="db7b5-919">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="db7b5-920">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="db7b5-920">Request Services</span></span>

<span data-ttu-id="db7b5-921">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="db7b5-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="db7b5-922">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="db7b5-923">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="db7b5-924">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="db7b5-925">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="db7b5-926">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="db7b5-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="db7b5-927">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="db7b5-928">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="db7b5-928">Design services for dependency injection</span></span>

<span data-ttu-id="db7b5-929">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="db7b5-929">Best practices are to:</span></span>

* <span data-ttu-id="db7b5-930">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="db7b5-931">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="db7b5-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="db7b5-932">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="db7b5-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="db7b5-933">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="db7b5-934">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="db7b5-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="db7b5-935">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="db7b5-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="db7b5-936">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="db7b5-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="db7b5-937">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="db7b5-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="db7b5-938">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="db7b5-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="db7b5-939">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="db7b5-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="db7b5-940">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="db7b5-940">Disposal of services</span></span>

<span data-ttu-id="db7b5-941">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="db7b5-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="db7b5-942">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="db7b5-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="db7b5-943">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="db7b5-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="db7b5-944">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="db7b5-944">In the following example:</span></span>

* <span data-ttu-id="db7b5-945">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="db7b5-946">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="db7b5-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="db7b5-947">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="db7b5-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="db7b5-948">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="db7b5-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="db7b5-949">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="db7b5-949">Default service container replacement</span></span>

<span data-ttu-id="db7b5-950">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="db7b5-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="db7b5-951">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="db7b5-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="db7b5-952">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="db7b5-952">Property injection</span></span>
* <span data-ttu-id="db7b5-953">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="db7b5-953">Injection based on name</span></span>
* <span data-ttu-id="db7b5-954">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="db7b5-954">Child containers</span></span>
* <span data-ttu-id="db7b5-955">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="db7b5-955">Custom lifetime management</span></span>
* <span data-ttu-id="db7b5-956">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="db7b5-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="db7b5-957">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="db7b5-957">Convention-based registration</span></span>

<span data-ttu-id="db7b5-958">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="db7b5-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="db7b5-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="db7b5-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="db7b5-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="db7b5-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="db7b5-961">Grace</span><span class="sxs-lookup"><span data-stu-id="db7b5-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="db7b5-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="db7b5-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="db7b5-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="db7b5-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="db7b5-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="db7b5-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="db7b5-965">Unity</span><span class="sxs-lookup"><span data-stu-id="db7b5-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="db7b5-966">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="db7b5-966">Thread safety</span></span>

<span data-ttu-id="db7b5-967">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="db7b5-968">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="db7b5-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="db7b5-969">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="db7b5-970">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="db7b5-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="db7b5-971">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="db7b5-971">Recommendations</span></span>

* <span data-ttu-id="db7b5-972">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="db7b5-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="db7b5-973">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="db7b5-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="db7b5-974">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="db7b5-975">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="db7b5-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="db7b5-976">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="db7b5-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="db7b5-977">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="db7b5-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="db7b5-978">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="db7b5-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="db7b5-979">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="db7b5-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="db7b5-980">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="db7b5-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="db7b5-981">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="db7b5-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="db7b5-982">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="db7b5-982">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    <span data-ttu-id="db7b5-983">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="db7b5-983">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * <span data-ttu-id="db7b5-984">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="db7b5-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="db7b5-985">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="db7b5-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="db7b5-986">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="db7b5-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="db7b5-987">Las excepciones son poco frecuentes; principalmente en casos especiales dentro del propio marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="db7b5-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="db7b5-988">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="db7b5-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="db7b5-989">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="db7b5-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db7b5-990">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="db7b5-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="db7b5-991">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="db7b5-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="db7b5-992">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="db7b5-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="db7b5-993">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="db7b5-993">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="db7b5-994">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db7b5-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
