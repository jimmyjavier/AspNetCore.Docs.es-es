---
title: 'title: Inserción de dependencias en ASP.NET Core author: rick-anderson description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 05/14/2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: db0a23e2db34de60308ea9be021a190278dee4aa
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271909"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="56d7a-103">"Blazor"</span><span class="sxs-lookup"><span data-stu-id="56d7a-103">'Blazor'</span></span>

<span data-ttu-id="56d7a-104">"Identity"</span><span class="sxs-lookup"><span data-stu-id="56d7a-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56d7a-105">"Let's Encrypt"</span><span class="sxs-lookup"><span data-stu-id="56d7a-105">'Let's Encrypt'</span></span>

<span data-ttu-id="56d7a-106">"Razor"</span><span class="sxs-lookup"><span data-stu-id="56d7a-106">'Razor'</span></span>

<span data-ttu-id="56d7a-107">"SignalR" uid: fundamentals/dependency-injection</span><span class="sxs-lookup"><span data-stu-id="56d7a-107">'SignalR' uid: fundamentals/dependency-injection</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="56d7a-108">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56d7a-108">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="56d7a-109">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="56d7a-109">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span> <span data-ttu-id="56d7a-110">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-110">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="56d7a-111">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-111">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="56d7a-112">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56d7a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="56d7a-113">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="56d7a-113">Overview of dependency injection</span></span> <span data-ttu-id="56d7a-114">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="56d7a-114">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="56d7a-115">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="56d7a-115">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="56d7a-116">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-116">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="56d7a-117">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-117">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="56d7a-118">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-118">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="56d7a-119">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="56d7a-119">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="56d7a-120">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-120">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="56d7a-121">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-121">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="56d7a-122">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-122">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="56d7a-123">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-123">This implementation is difficult to unit test.</span></span> <span data-ttu-id="56d7a-124">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="56d7a-124">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="56d7a-125">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="56d7a-125">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="56d7a-126">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-126">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="56d7a-127">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-127">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="56d7a-128">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-128">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="56d7a-129">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-129">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="56d7a-130">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="56d7a-130">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="56d7a-131">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="56d7a-131">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="56d7a-132">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="56d7a-132">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="56d7a-133">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-133">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="56d7a-134">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-134">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="56d7a-135">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="56d7a-135">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="56d7a-136">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-136">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="56d7a-137">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="56d7a-137">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="56d7a-138">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-138">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="56d7a-139">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-139">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="56d7a-140">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-140">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="56d7a-141">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-141">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="56d7a-142">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="56d7a-142">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="56d7a-143">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-143">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="56d7a-144">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="56d7a-144">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="56d7a-145">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="56d7a-145">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="56d7a-146">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="56d7a-146">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="56d7a-147">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="56d7a-147">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="56d7a-148">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="56d7a-148">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="56d7a-149">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-149">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="56d7a-150">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="56d7a-150">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="56d7a-151">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="56d7a-151">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="56d7a-152">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-152">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="56d7a-153">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="56d7a-153">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="56d7a-154">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="56d7a-154">Services injected into Startup</span></span> <span data-ttu-id="56d7a-155">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="56d7a-155">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="56d7a-156">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-156">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="56d7a-157">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-157">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="56d7a-158">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="56d7a-158">Framework-provided services</span></span> | <span data-ttu-id="56d7a-159">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="56d7a-159">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="56d7a-160">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="56d7a-160">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="56d7a-161">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="56d7a-161">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="56d7a-162">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="56d7a-162">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="56d7a-163">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="56d7a-163">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="56d7a-164">Período de duración</span><span class="sxs-lookup"><span data-stu-id="56d7a-164">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="56d7a-165">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-165">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="56d7a-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-166">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="56d7a-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="56d7a-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="56d7a-169">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-169">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="56d7a-170">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-170">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="56d7a-171">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-171">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="56d7a-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="56d7a-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="56d7a-174">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-174">Singleton</span></span>

<span data-ttu-id="56d7a-175">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-175">Transient</span></span> <span data-ttu-id="56d7a-176">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-176">Singleton</span></span>

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

<span data-ttu-id="56d7a-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-177">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="56d7a-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-178">Singleton</span></span>

<span data-ttu-id="56d7a-179">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="56d7a-179">Register additional services with extension methods</span></span> <span data-ttu-id="56d7a-180">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-180">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="56d7a-181">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="56d7a-181">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="56d7a-182">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="56d7a-182">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="56d7a-183">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="56d7a-183">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="56d7a-184">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="56d7a-184">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="56d7a-185">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-185">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="56d7a-186">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-186">Transient</span></span> <span data-ttu-id="56d7a-187">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="56d7a-187">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="56d7a-188">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="56d7a-188">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="56d7a-189">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="56d7a-189">Scoped</span></span>

<span data-ttu-id="56d7a-190">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="56d7a-190">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="56d7a-191">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-191">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="56d7a-192">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="56d7a-192">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="56d7a-193">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-193">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="56d7a-194">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-194">Singleton</span></span> <span data-ttu-id="56d7a-195">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-195">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="56d7a-196">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="56d7a-196">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="56d7a-197">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-197">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="56d7a-198">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-198">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="56d7a-199">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="56d7a-199">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="56d7a-200">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="56d7a-200">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="56d7a-201">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="56d7a-201">Service registration methods</span></span> | <span data-ttu-id="56d7a-202">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-202">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="56d7a-203">Método</span><span class="sxs-lookup"><span data-stu-id="56d7a-203">Method</span></span> | <span data-ttu-id="56d7a-204">Automático</span><span class="sxs-lookup"><span data-stu-id="56d7a-204">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="56d7a-205">objeto</span><span class="sxs-lookup"><span data-stu-id="56d7a-205">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="56d7a-206">eliminación</span><span class="sxs-lookup"><span data-stu-id="56d7a-206">disposal</span></span> | <span data-ttu-id="56d7a-207">Múltiple</span><span class="sxs-lookup"><span data-stu-id="56d7a-207">Multiple</span></span> | <span data-ttu-id="56d7a-208">implementaciones</span><span class="sxs-lookup"><span data-stu-id="56d7a-208">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="56d7a-209">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="56d7a-209">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="56d7a-210">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56d7a-210">Example:</span></span> | <span data-ttu-id="56d7a-211">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-211">Yes</span></span> | <span data-ttu-id="56d7a-212">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-212">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="56d7a-213">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-213">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="56d7a-214">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-214">Examples:</span></span> | <span data-ttu-id="56d7a-215">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-215">Yes</span></span> | <span data-ttu-id="56d7a-216">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-216">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="56d7a-217">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-217">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="56d7a-218">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56d7a-218">Example:</span></span> | <span data-ttu-id="56d7a-219">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-219">Yes</span></span> | <span data-ttu-id="56d7a-220">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-220">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="56d7a-221">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-221">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="56d7a-222">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-222">Examples:</span></span> | <span data-ttu-id="56d7a-223">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-223">No</span></span> | <span data-ttu-id="56d7a-224">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-224">Yes</span></span> |

<span data-ttu-id="56d7a-225">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-225">Yes</span></span> <span data-ttu-id="56d7a-226">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-226">Examples:</span></span>

<span data-ttu-id="56d7a-227">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-227">No</span></span>

<span data-ttu-id="56d7a-228">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-228">No</span></span> <span data-ttu-id="56d7a-229">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-229">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="56d7a-230">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="56d7a-230">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="56d7a-231">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="56d7a-231">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="56d7a-232">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="56d7a-232">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="56d7a-233">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-233">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="56d7a-234">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="56d7a-234">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="56d7a-235">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="56d7a-235">For more information, see:</span></span> <span data-ttu-id="56d7a-236">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-236">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="56d7a-237">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-237">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="56d7a-238">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-238">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="56d7a-239">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-239">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="56d7a-240">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-240">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="56d7a-241">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-241">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="56d7a-242">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-242">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="56d7a-243">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="56d7a-243">Constructor injection behavior</span></span>

<span data-ttu-id="56d7a-244">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-244">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="56d7a-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-245"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="56d7a-246">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-246">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="56d7a-247">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="56d7a-247">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="56d7a-248">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-248">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="56d7a-249">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="56d7a-249">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="56d7a-250">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-250">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="56d7a-251">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="56d7a-251">Entity Framework contexts</span></span> <span data-ttu-id="56d7a-252">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-252">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="56d7a-253">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-253">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="56d7a-254">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-254">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="56d7a-255">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="56d7a-255">Lifetime and registration options</span></span> <span data-ttu-id="56d7a-256">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-256">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="56d7a-257">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="56d7a-257">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="56d7a-258">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-258">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="56d7a-259">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="56d7a-259">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="56d7a-260">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-260">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="56d7a-261">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="56d7a-261">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="56d7a-262">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-262">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="56d7a-263">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-263">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="56d7a-264">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-264">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="56d7a-265">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-265">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="56d7a-266">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-266">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="56d7a-267">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-267">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="56d7a-268">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="56d7a-268">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="56d7a-269">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-269">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="56d7a-270">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="56d7a-270">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="56d7a-271">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="56d7a-271">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="56d7a-272">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-272">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="56d7a-273">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="56d7a-273">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="56d7a-274">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-274">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="56d7a-275">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="56d7a-275">**First request:**</span></span>

<span data-ttu-id="56d7a-276">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="56d7a-276">Controller operations:</span></span>

<span data-ttu-id="56d7a-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="56d7a-277">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="56d7a-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="56d7a-278">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="56d7a-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-279">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="56d7a-280">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-280">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="56d7a-281">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-281">`OperationService` operations:</span></span>

<span data-ttu-id="56d7a-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="56d7a-282">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="56d7a-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="56d7a-283">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="56d7a-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-284">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="56d7a-285">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-285">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="56d7a-286">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="56d7a-286">**Second request:**</span></span>

<span data-ttu-id="56d7a-287">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="56d7a-287">Controller operations:</span></span>

<span data-ttu-id="56d7a-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="56d7a-288">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="56d7a-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="56d7a-289">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="56d7a-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="56d7a-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="56d7a-292">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-292">`OperationService` operations:</span></span>

* <span data-ttu-id="56d7a-293">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="56d7a-293">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="56d7a-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="56d7a-294">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="56d7a-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="56d7a-296">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="56d7a-297">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-297">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="56d7a-298">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="56d7a-298">*Transient* objects are always different.</span></span>

<span data-ttu-id="56d7a-299">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-299">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="56d7a-300">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-300">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="56d7a-301">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-301">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="56d7a-302">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-302">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="56d7a-303">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="56d7a-303">Call services from main</span></span>

* <span data-ttu-id="56d7a-304">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-304">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="56d7a-305">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="56d7a-305">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="56d7a-306">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-306">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="56d7a-307">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="56d7a-307">Scope validation</span></span>

<span data-ttu-id="56d7a-308">Cuando la aplicación se ejecuta en el entorno de desarrollo y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56d7a-308">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="56d7a-309">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="56d7a-309">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="56d7a-310">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="56d7a-310">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="56d7a-311">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-311">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="56d7a-312">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="56d7a-312">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="56d7a-313">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="56d7a-313">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="56d7a-314">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="56d7a-314">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="56d7a-315">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-315">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="56d7a-316">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-316">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="56d7a-317">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="56d7a-317">Request Services</span></span> <span data-ttu-id="56d7a-318">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="56d7a-318">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="56d7a-319">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-319">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="56d7a-320">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-320">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="56d7a-321">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-321">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="56d7a-322">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-322">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span>
* <span data-ttu-id="56d7a-323">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="56d7a-323">This yields classes that are easier to test.</span></span> <span data-ttu-id="56d7a-324">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-324">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="56d7a-325">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="56d7a-325">Design services for dependency injection</span></span> <span data-ttu-id="56d7a-326">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="56d7a-326">Best practices are to:</span></span>
* <span data-ttu-id="56d7a-327">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-327">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="56d7a-328">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="56d7a-328">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="56d7a-329">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="56d7a-329">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="56d7a-330">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-330">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="56d7a-331">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="56d7a-331">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="56d7a-332">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="56d7a-332">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="56d7a-333">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="56d7a-333">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="56d7a-334">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="56d7a-334">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="56d7a-335">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="56d7a-335">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="56d7a-336">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="56d7a-336">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="56d7a-337">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="56d7a-337">Disposal of services</span></span>
* <span data-ttu-id="56d7a-338">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="56d7a-338">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="56d7a-339">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-339">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="56d7a-340">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="56d7a-340">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="56d7a-341">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56d7a-341">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="56d7a-342">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-342">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="56d7a-343">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-343">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="56d7a-344">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="56d7a-344">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="56d7a-345">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-345">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="56d7a-346">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="56d7a-346">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="56d7a-347">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="56d7a-347">Transient, limited lifetime</span></span>

<span data-ttu-id="56d7a-348">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="56d7a-348">**Scenario**</span></span> <span data-ttu-id="56d7a-349">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="56d7a-349">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="56d7a-350">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="56d7a-350">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="56d7a-351">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="56d7a-351">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="56d7a-352">**Solución**</span><span class="sxs-lookup"><span data-stu-id="56d7a-352">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="56d7a-353">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="56d7a-353">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="56d7a-354">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="56d7a-354">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="56d7a-355">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="56d7a-355">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="56d7a-356">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-356">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="56d7a-357">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-357">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="56d7a-358">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="56d7a-358">Shared Instance, limited lifetime</span></span> <span data-ttu-id="56d7a-359">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="56d7a-359">**Scenario**</span></span> <span data-ttu-id="56d7a-360">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="56d7a-360">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="56d7a-361">**Solución**</span><span class="sxs-lookup"><span data-stu-id="56d7a-361">**Solution**</span></span>

* <span data-ttu-id="56d7a-362">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="56d7a-362">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="56d7a-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-363">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="56d7a-364">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-364">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="56d7a-365">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="56d7a-365">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="56d7a-366">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="56d7a-366">General Guidelines</span></span> <span data-ttu-id="56d7a-367">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-367">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="56d7a-368">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="56d7a-368">Use the factory pattern instead.</span></span> <span data-ttu-id="56d7a-369">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="56d7a-369">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="56d7a-370">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="56d7a-370">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="56d7a-371">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-371">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="56d7a-372">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="56d7a-372">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="56d7a-373">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-373">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="56d7a-374">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-374">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="56d7a-375">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="56d7a-375">Default service container replacement</span></span>
* <span data-ttu-id="56d7a-376">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-376">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="56d7a-377">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-377">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="56d7a-378">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="56d7a-378">Property injection</span></span>

<span data-ttu-id="56d7a-379">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="56d7a-379">Injection based on name</span></span>

* <span data-ttu-id="56d7a-380">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="56d7a-380">Child containers</span></span>
* <span data-ttu-id="56d7a-381">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="56d7a-381">Custom lifetime management</span></span>
* <span data-ttu-id="56d7a-382">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="56d7a-382">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="56d7a-383">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="56d7a-383">Convention-based registration</span></span>
* <span data-ttu-id="56d7a-384">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="56d7a-384">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="56d7a-385">Autofac</span><span class="sxs-lookup"><span data-stu-id="56d7a-385">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="56d7a-386">DryIoc</span><span class="sxs-lookup"><span data-stu-id="56d7a-386">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="56d7a-387">Grace</span><span class="sxs-lookup"><span data-stu-id="56d7a-387">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="56d7a-388">LightInject</span><span class="sxs-lookup"><span data-stu-id="56d7a-388">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="56d7a-389">Lamar</span><span class="sxs-lookup"><span data-stu-id="56d7a-389">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="56d7a-390">Stashbox</span><span class="sxs-lookup"><span data-stu-id="56d7a-390">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="56d7a-391">Unity</span><span class="sxs-lookup"><span data-stu-id="56d7a-391">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="56d7a-392">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="56d7a-392">Thread safety</span></span>

* <span data-ttu-id="56d7a-393">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-393">Create thread-safe singleton services.</span></span> <span data-ttu-id="56d7a-394">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="56d7a-394">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="56d7a-395">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-395">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="56d7a-396">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="56d7a-396">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="56d7a-397">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="56d7a-397">Recommendations</span></span> <span data-ttu-id="56d7a-398">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-398">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="56d7a-399">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="56d7a-399">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="56d7a-400">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-400">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="56d7a-401">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-401">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="56d7a-402">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="56d7a-402">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

  <span data-ttu-id="56d7a-403">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="56d7a-403">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

  <span data-ttu-id="56d7a-404">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-404">It's better to request the actual item via DI.</span></span>

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

* <span data-ttu-id="56d7a-405">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="56d7a-405">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span> <span data-ttu-id="56d7a-406">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-406">Avoid using the *service locator pattern*.</span></span>

* <span data-ttu-id="56d7a-407">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="56d7a-407">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

<span data-ttu-id="56d7a-408">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="56d7a-408">**Incorrect:**</span></span> <span data-ttu-id="56d7a-409">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="56d7a-409">**Correct**:</span></span>

<span data-ttu-id="56d7a-410">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="56d7a-410">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="56d7a-411">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="56d7a-411">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="56d7a-412">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="56d7a-412">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="56d7a-413">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="56d7a-413">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="56d7a-414">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-414">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="56d7a-415">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="56d7a-415">DI is an *alternative* to static/global object access patterns.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56d7a-416">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-416">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="56d7a-417">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="56d7a-417">Recommended patterns for multi-tenancy in DI</span></span>
* <span data-ttu-id="56d7a-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) proporciona servicios multiinquilino.</span><span class="sxs-lookup"><span data-stu-id="56d7a-418">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span>
* <span data-ttu-id="56d7a-419">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="56d7a-419">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>
* <span data-ttu-id="56d7a-420">Vea las aplicaciones de ejemplo en https://github.com/OrchardCMS/OrchardCore.Samples para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="56d7a-420">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>
* <span data-ttu-id="56d7a-421">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="56d7a-421">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[<span data-ttu-id="56d7a-422">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56d7a-422">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)

[<span data-ttu-id="56d7a-423">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="56d7a-423">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)

[<span data-ttu-id="56d7a-424">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="56d7a-424">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)

## <a name="overview-of-dependency-injection"></a>[<span data-ttu-id="56d7a-425">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="56d7a-425">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)

[<span data-ttu-id="56d7a-426">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56d7a-426">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) <span data-ttu-id="56d7a-427">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-427">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

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

<span data-ttu-id="56d7a-428">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-428">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span> <span data-ttu-id="56d7a-429">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56d7a-429">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

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

<span data-ttu-id="56d7a-430">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="56d7a-430">Overview of dependency injection</span></span> <span data-ttu-id="56d7a-431">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="56d7a-431">A *dependency* is any object that another object requires.</span></span>

* <span data-ttu-id="56d7a-432">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="56d7a-432">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>
* <span data-ttu-id="56d7a-433">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-433">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="56d7a-434">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-434">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>
* <span data-ttu-id="56d7a-435">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-435">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="56d7a-436">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="56d7a-436">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

<span data-ttu-id="56d7a-437">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-437">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>

* <span data-ttu-id="56d7a-438">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-438">If `MyDependency` has dependencies, they must be configured by the class.</span></span>
* <span data-ttu-id="56d7a-439">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-439">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span> <span data-ttu-id="56d7a-440">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-440">This implementation is difficult to unit test.</span></span> <span data-ttu-id="56d7a-441">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="56d7a-441">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>
* <span data-ttu-id="56d7a-442">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="56d7a-442">Dependency injection addresses these problems through:</span></span> <span data-ttu-id="56d7a-443">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-443">The use of an interface or base class to abstract the dependency implementation.</span></span>

<span data-ttu-id="56d7a-444">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-444">Registration of the dependency in a service container.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="56d7a-445">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-445">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="56d7a-446">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-446">Services are registered in the app's `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="56d7a-447">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="56d7a-447">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="56d7a-448">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="56d7a-448">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span> <span data-ttu-id="56d7a-449">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="56d7a-449">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span> <span data-ttu-id="56d7a-450">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-450">This interface is implemented by a concrete type, `MyDependency`:</span></span>

<span data-ttu-id="56d7a-451">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-451">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="56d7a-452">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="56d7a-452">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="56d7a-453">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-453">Each requested dependency in turn requests its own dependencies.</span></span>

<span data-ttu-id="56d7a-454">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="56d7a-454">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="56d7a-455">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-455">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span> <span data-ttu-id="56d7a-456">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-456">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="56d7a-457">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-457">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="56d7a-458">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-458">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span> <span data-ttu-id="56d7a-459">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="56d7a-459">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span> <span data-ttu-id="56d7a-460">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-460">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="56d7a-461">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="56d7a-461">The registration scopes the service lifetime to the lifetime of a single request.</span></span>

<span data-ttu-id="56d7a-462">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="56d7a-462">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

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

<span data-ttu-id="56d7a-463">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="56d7a-463">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="56d7a-464">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="56d7a-464">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span>

<span data-ttu-id="56d7a-465">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="56d7a-465">We recommended that apps follow this convention.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="56d7a-466">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-466">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="56d7a-467">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="56d7a-467">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="56d7a-468">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="56d7a-468">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="56d7a-469">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-469">The field is used to access the service as necessary throughout the class.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="56d7a-470">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="56d7a-470">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

<span data-ttu-id="56d7a-471">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="56d7a-471">Services injected into Startup</span></span> <span data-ttu-id="56d7a-472">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="56d7a-472">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span> <span data-ttu-id="56d7a-473">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-473">Services can be injected into `Startup.Configure`:</span></span> <span data-ttu-id="56d7a-474">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-474">For more information, see <xref:fundamentals/startup>.</span></span>

| <span data-ttu-id="56d7a-475">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="56d7a-475">Framework-provided services</span></span> | <span data-ttu-id="56d7a-476">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="56d7a-476">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="56d7a-477">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="56d7a-477">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="56d7a-478">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="56d7a-478">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="56d7a-479">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="56d7a-479">A small sample of framework-registered services is listed in the following table.</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="56d7a-480">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="56d7a-480">Service Type</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="56d7a-481">Período de duración</span><span class="sxs-lookup"><span data-stu-id="56d7a-481">Lifetime</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="56d7a-482">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-482">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="56d7a-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-483">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="56d7a-484">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-484">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="56d7a-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-485">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="56d7a-486">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="56d7a-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="56d7a-488">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-488">Transient</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="56d7a-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-489">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="56d7a-490">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-490">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="56d7a-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-491">Singleton</span></span>

<span data-ttu-id="56d7a-492">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-492">Transient</span></span> <span data-ttu-id="56d7a-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-493">Singleton</span></span>

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

<span data-ttu-id="56d7a-494">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-494">Singleton</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="56d7a-495">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-495">Singleton</span></span>

<span data-ttu-id="56d7a-496">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="56d7a-496">Register additional services with extension methods</span></span> <span data-ttu-id="56d7a-497">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-497">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>

### <a name="transient"></a><span data-ttu-id="56d7a-498">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="56d7a-498">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

<span data-ttu-id="56d7a-499">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="56d7a-499">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span> <span data-ttu-id="56d7a-500">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="56d7a-500">Service lifetimes</span></span>

### <a name="scoped"></a><span data-ttu-id="56d7a-501">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="56d7a-501">Choose an appropriate lifetime for each registered service.</span></span>

<span data-ttu-id="56d7a-502">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-502">ASP.NET Core services can be configured with the following lifetimes:</span></span>

> [!WARNING]
> <span data-ttu-id="56d7a-503">Transitorio</span><span class="sxs-lookup"><span data-stu-id="56d7a-503">Transient</span></span> <span data-ttu-id="56d7a-504">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="56d7a-504">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="56d7a-505">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="56d7a-505">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="singleton"></a><span data-ttu-id="56d7a-506">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="56d7a-506">Scoped</span></span>

<span data-ttu-id="56d7a-507">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="56d7a-507">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span> <span data-ttu-id="56d7a-508">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="56d7a-509">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="56d7a-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="56d7a-510">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

> [!WARNING]
> <span data-ttu-id="56d7a-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="56d7a-511">Singleton</span></span> <span data-ttu-id="56d7a-512">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="56d7a-513">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="56d7a-513">Every subsequent request uses the same instance.</span></span>

<span data-ttu-id="56d7a-514">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span>

| <span data-ttu-id="56d7a-515">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="56d7a-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span> | <span data-ttu-id="56d7a-516">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="56d7a-516">It's dangerous to resolve a scoped service from a singleton.</span></span><br><span data-ttu-id="56d7a-517">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="56d7a-517">It may cause the service to have incorrect state when processing subsequent requests.</span></span><br><span data-ttu-id="56d7a-518">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="56d7a-518">Service registration methods</span></span> | <span data-ttu-id="56d7a-519">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-519">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span><br><span data-ttu-id="56d7a-520">Método</span><span class="sxs-lookup"><span data-stu-id="56d7a-520">Method</span></span> | <span data-ttu-id="56d7a-521">Automático</span><span class="sxs-lookup"><span data-stu-id="56d7a-521">Automatic</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="56d7a-522">objeto</span><span class="sxs-lookup"><span data-stu-id="56d7a-522">object</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="56d7a-523">eliminación</span><span class="sxs-lookup"><span data-stu-id="56d7a-523">disposal</span></span> | <span data-ttu-id="56d7a-524">Múltiple</span><span class="sxs-lookup"><span data-stu-id="56d7a-524">Multiple</span></span> | <span data-ttu-id="56d7a-525">implementaciones</span><span class="sxs-lookup"><span data-stu-id="56d7a-525">implementations</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="56d7a-526">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="56d7a-526">Pass args</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="56d7a-527">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56d7a-527">Example:</span></span> | <span data-ttu-id="56d7a-528">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-528">Yes</span></span> | <span data-ttu-id="56d7a-529">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-529">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="56d7a-530">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-530">No</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="56d7a-531">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-531">Examples:</span></span> | <span data-ttu-id="56d7a-532">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-532">Yes</span></span> | <span data-ttu-id="56d7a-533">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-533">Yes</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="56d7a-534">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-534">Yes</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="56d7a-535">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56d7a-535">Example:</span></span> | <span data-ttu-id="56d7a-536">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-536">Yes</span></span> | <span data-ttu-id="56d7a-537">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-537">No</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="56d7a-538">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-538">No</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="56d7a-539">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-539">Examples:</span></span> | <span data-ttu-id="56d7a-540">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-540">No</span></span> | <span data-ttu-id="56d7a-541">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-541">Yes</span></span> |

<span data-ttu-id="56d7a-542">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-542">Yes</span></span> <span data-ttu-id="56d7a-543">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-543">Examples:</span></span>

<span data-ttu-id="56d7a-544">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-544">No</span></span>

<span data-ttu-id="56d7a-545">No</span><span class="sxs-lookup"><span data-stu-id="56d7a-545">No</span></span> <span data-ttu-id="56d7a-546">Sí</span><span class="sxs-lookup"><span data-stu-id="56d7a-546">Yes</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="56d7a-547">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="56d7a-547">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="56d7a-548">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="56d7a-548">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span> <span data-ttu-id="56d7a-549">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="56d7a-549">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span> <span data-ttu-id="56d7a-550">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-550">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="56d7a-551">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="56d7a-551">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

<span data-ttu-id="56d7a-552">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="56d7a-552">For more information, see:</span></span> <span data-ttu-id="56d7a-553">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-553">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="56d7a-554">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-554">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="56d7a-555">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-555">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span>

<span data-ttu-id="56d7a-556">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-556">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="56d7a-557">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-557">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="56d7a-558">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-558">The second line registers `MyDep` for `IMyDep2`.</span></span>

<span data-ttu-id="56d7a-559">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-559">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

<span data-ttu-id="56d7a-560">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="56d7a-560">Constructor injection behavior</span></span>

<span data-ttu-id="56d7a-561">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="56d7a-561">Services can be resolved by two mechanisms:</span></span> <span data-ttu-id="56d7a-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-562"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="56d7a-563">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-563">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="56d7a-564">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="56d7a-564">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span> <span data-ttu-id="56d7a-565">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="56d7a-565">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span> <span data-ttu-id="56d7a-566">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="56d7a-566">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="56d7a-567">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-567">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

<span data-ttu-id="56d7a-568">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="56d7a-568">Entity Framework contexts</span></span> <span data-ttu-id="56d7a-569">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-569">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="56d7a-570">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-570">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="56d7a-571">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-571">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="56d7a-572">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="56d7a-572">Lifetime and registration options</span></span> <span data-ttu-id="56d7a-573">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-573">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span>

* <span data-ttu-id="56d7a-574">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="56d7a-574">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span> <span data-ttu-id="56d7a-575">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-575">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="56d7a-576">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="56d7a-576">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>
* <span data-ttu-id="56d7a-577">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-577">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="56d7a-578">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="56d7a-578">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>
* <span data-ttu-id="56d7a-579">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-579">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="56d7a-580">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-580">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="56d7a-581">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-581">The new instance yields a different `OperationId`.</span></span> <span data-ttu-id="56d7a-582">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-582">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span>

<span data-ttu-id="56d7a-583">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-583">Across client requests, both services share a different `OperationId` value.</span></span> <span data-ttu-id="56d7a-584">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-584">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span> <span data-ttu-id="56d7a-585">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="56d7a-585">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="56d7a-586">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-586">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span>

<span data-ttu-id="56d7a-587">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="56d7a-587">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="56d7a-588">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="56d7a-588">The sample app demonstrates object lifetimes within and between individual requests.</span></span>

<span data-ttu-id="56d7a-589">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-589">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span>  
<span data-ttu-id="56d7a-590">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="56d7a-590">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>  
<span data-ttu-id="56d7a-591">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-591">Two following output shows the results of two requests:</span></span>  
<span data-ttu-id="56d7a-592">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="56d7a-592">**First request:**</span></span>

<span data-ttu-id="56d7a-593">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="56d7a-593">Controller operations:</span></span>

<span data-ttu-id="56d7a-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="56d7a-594">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="56d7a-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="56d7a-595">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="56d7a-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-596">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="56d7a-597">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-597">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="56d7a-598">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-598">`OperationService` operations:</span></span>

<span data-ttu-id="56d7a-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="56d7a-599">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>

<span data-ttu-id="56d7a-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="56d7a-600">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="56d7a-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-601">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="56d7a-602">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-602">Instance: 00000000-0000-0000-0000-000000000000</span></span>  
<span data-ttu-id="56d7a-603">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="56d7a-603">**Second request:**</span></span>

<span data-ttu-id="56d7a-604">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="56d7a-604">Controller operations:</span></span>

<span data-ttu-id="56d7a-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="56d7a-605">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="56d7a-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="56d7a-606">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="56d7a-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-607">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="56d7a-608">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-608">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="56d7a-609">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-609">`OperationService` operations:</span></span>

* <span data-ttu-id="56d7a-610">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="56d7a-610">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span> <span data-ttu-id="56d7a-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="56d7a-611">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span> <span data-ttu-id="56d7a-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="56d7a-612">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>
* <span data-ttu-id="56d7a-613">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="56d7a-613">Instance: 00000000-0000-0000-0000-000000000000</span></span>
* <span data-ttu-id="56d7a-614">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-614">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="56d7a-615">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="56d7a-615">*Transient* objects are always different.</span></span>

<span data-ttu-id="56d7a-616">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-616">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="56d7a-617">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-617">A new instance is provided to each service request and client request.</span></span> <span data-ttu-id="56d7a-618">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-618">*Scoped* objects are the same within a client request but different across client requests.</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="56d7a-619">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-619">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="56d7a-620">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="56d7a-620">Call services from main</span></span>

* <span data-ttu-id="56d7a-621">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-621">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span>
* <span data-ttu-id="56d7a-622">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="56d7a-622">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="56d7a-623">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="56d7a-623">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span> <span data-ttu-id="56d7a-624">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="56d7a-624">Scope validation</span></span>

<span data-ttu-id="56d7a-625">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56d7a-625">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span> <span data-ttu-id="56d7a-626">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="56d7a-626">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span> <span data-ttu-id="56d7a-627">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="56d7a-627">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="56d7a-628">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-628">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span>

## <a name="request-services"></a><span data-ttu-id="56d7a-629">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="56d7a-629">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="56d7a-630">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="56d7a-630">Scoped services are disposed by the container that created them.</span></span>

<span data-ttu-id="56d7a-631">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="56d7a-631">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="56d7a-632">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-632">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="56d7a-633">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-633">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span> <span data-ttu-id="56d7a-634">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="56d7a-634">Request Services</span></span> <span data-ttu-id="56d7a-635">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="56d7a-635">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

> [!NOTE]
> <span data-ttu-id="56d7a-636">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-636">Request Services represent the services configured and requested as part of the app.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="56d7a-637">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-637">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="56d7a-638">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-638">Generally, the app shouldn't use these properties directly.</span></span>

* <span data-ttu-id="56d7a-639">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-639">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span>
* <span data-ttu-id="56d7a-640">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="56d7a-640">This yields classes that are easier to test.</span></span> <span data-ttu-id="56d7a-641">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-641">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>
* <span data-ttu-id="56d7a-642">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="56d7a-642">Design services for dependency injection</span></span> <span data-ttu-id="56d7a-643">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="56d7a-643">Best practices are to:</span></span>
* <span data-ttu-id="56d7a-644">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-644">Design services to use dependency injection to obtain their dependencies.</span></span>

<span data-ttu-id="56d7a-645">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="56d7a-645">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="56d7a-646">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="56d7a-646">Design apps to use singleton services instead, which avoid creating global state.</span></span> <span data-ttu-id="56d7a-647">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-647">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="56d7a-648">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="56d7a-648">Direct instantiation couples the code to a particular implementation.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="56d7a-649">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="56d7a-649">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="56d7a-650">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="56d7a-650">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="56d7a-651">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="56d7a-651">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span>

<span data-ttu-id="56d7a-652">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="56d7a-652">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

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

<span data-ttu-id="56d7a-653">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="56d7a-653">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

* <span data-ttu-id="56d7a-654">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="56d7a-654">Disposal of services</span></span>
* <span data-ttu-id="56d7a-655">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="56d7a-655">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span>
* <span data-ttu-id="56d7a-656">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="56d7a-656">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>
* <span data-ttu-id="56d7a-657">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="56d7a-657">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="56d7a-658">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56d7a-658">In the following example:</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="56d7a-659">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-659">The service instances aren't created by the service container.</span></span>

<span data-ttu-id="56d7a-660">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-660">The intended service lifetimes aren't known by the framework.</span></span>

<span data-ttu-id="56d7a-661">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="56d7a-661">The framework doesn't dispose of the services automatically.</span></span>

* <span data-ttu-id="56d7a-662">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56d7a-662">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>
* <span data-ttu-id="56d7a-663">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="56d7a-663">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="56d7a-664">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="56d7a-664">Transient, limited lifetime</span></span>

<span data-ttu-id="56d7a-665">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="56d7a-665">**Scenario**</span></span> <span data-ttu-id="56d7a-666">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="56d7a-666">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span> <span data-ttu-id="56d7a-667">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="56d7a-667">The instance is resolved in the root scope.</span></span>

* <span data-ttu-id="56d7a-668">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="56d7a-668">The instance should be disposed before the scope ends.</span></span>
* <span data-ttu-id="56d7a-669">**Solución**</span><span class="sxs-lookup"><span data-stu-id="56d7a-669">**Solution**</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="56d7a-670">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="56d7a-670">Use the factory pattern to create an instance outside of the parent scope.</span></span>

<span data-ttu-id="56d7a-671">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="56d7a-671">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span>

<span data-ttu-id="56d7a-672">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="56d7a-672">If the final type has other dependencies, the factory can:</span></span>

<span data-ttu-id="56d7a-673">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-673">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>

<span data-ttu-id="56d7a-674">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-674">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span> <span data-ttu-id="56d7a-675">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="56d7a-675">Shared Instance, limited lifetime</span></span> <span data-ttu-id="56d7a-676">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="56d7a-676">**Scenario**</span></span> <span data-ttu-id="56d7a-677">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="56d7a-677">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="56d7a-678">**Solución**</span><span class="sxs-lookup"><span data-stu-id="56d7a-678">**Solution**</span></span>

* <span data-ttu-id="56d7a-679">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="56d7a-679">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="56d7a-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-680">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span>
* <span data-ttu-id="56d7a-681">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-681">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="56d7a-682">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="56d7a-682">Dispose the scope when the lifetime should end.</span></span>
* <span data-ttu-id="56d7a-683">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="56d7a-683">General Guidelines</span></span> <span data-ttu-id="56d7a-684">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="56d7a-684">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span>
* <span data-ttu-id="56d7a-685">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="56d7a-685">Use the factory pattern instead.</span></span> <span data-ttu-id="56d7a-686">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="56d7a-686">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="56d7a-687">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="56d7a-687">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>

<span data-ttu-id="56d7a-688">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-688">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="56d7a-689">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="56d7a-689">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>

* <span data-ttu-id="56d7a-690">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-690">Scopes should be used to control lifetimes of services.</span></span>
* <span data-ttu-id="56d7a-691">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-691">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>
* <span data-ttu-id="56d7a-692">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="56d7a-692">Default service container replacement</span></span>
* <span data-ttu-id="56d7a-693">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="56d7a-693">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span>
* <span data-ttu-id="56d7a-694">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="56d7a-694">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>
* <span data-ttu-id="56d7a-695">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="56d7a-695">Property injection</span></span>

<span data-ttu-id="56d7a-696">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="56d7a-696">Injection based on name</span></span>

* <span data-ttu-id="56d7a-697">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="56d7a-697">Child containers</span></span>
* <span data-ttu-id="56d7a-698">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="56d7a-698">Custom lifetime management</span></span>
* <span data-ttu-id="56d7a-699">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="56d7a-699">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="56d7a-700">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="56d7a-700">Convention-based registration</span></span>
* <span data-ttu-id="56d7a-701">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="56d7a-701">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>
* [<span data-ttu-id="56d7a-702">Autofac</span><span class="sxs-lookup"><span data-stu-id="56d7a-702">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="56d7a-703">DryIoc</span><span class="sxs-lookup"><span data-stu-id="56d7a-703">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>[<span data-ttu-id="56d7a-704">Grace</span><span class="sxs-lookup"><span data-stu-id="56d7a-704">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)

[<span data-ttu-id="56d7a-705">LightInject</span><span class="sxs-lookup"><span data-stu-id="56d7a-705">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection) [<span data-ttu-id="56d7a-706">Lamar</span><span class="sxs-lookup"><span data-stu-id="56d7a-706">Lamar</span></span>](https://jasperfx.github.io/lamar/)

[<span data-ttu-id="56d7a-707">Stashbox</span><span class="sxs-lookup"><span data-stu-id="56d7a-707">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection) [<span data-ttu-id="56d7a-708">Unity</span><span class="sxs-lookup"><span data-stu-id="56d7a-708">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="recommendations"></a><span data-ttu-id="56d7a-709">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="56d7a-709">Thread safety</span></span>

* <span data-ttu-id="56d7a-710">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-710">Create thread-safe singleton services.</span></span> <span data-ttu-id="56d7a-711">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="56d7a-711">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

* <span data-ttu-id="56d7a-712">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-712">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="56d7a-713">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="56d7a-713">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span> <span data-ttu-id="56d7a-714">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="56d7a-714">Recommendations</span></span> <span data-ttu-id="56d7a-715">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="56d7a-715">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="56d7a-716">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="56d7a-716">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="56d7a-717">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-717">Avoid storing data and configuration directly in the service container.</span></span>

* <span data-ttu-id="56d7a-718">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="56d7a-718">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span>

  * <span data-ttu-id="56d7a-719">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="56d7a-719">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span>

    <span data-ttu-id="56d7a-720">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="56d7a-720">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span>

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

    <span data-ttu-id="56d7a-721">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="56d7a-721">It's better to request the actual item via DI.</span></span>

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

  * <span data-ttu-id="56d7a-722">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="56d7a-722">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="56d7a-723">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="56d7a-723">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

<span data-ttu-id="56d7a-724">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="56d7a-724">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span> <span data-ttu-id="56d7a-725">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="56d7a-725">**Incorrect:**</span></span>

<span data-ttu-id="56d7a-726">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="56d7a-726">**Correct**:</span></span> <span data-ttu-id="56d7a-727">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="56d7a-727">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56d7a-728">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="56d7a-728">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* <span data-ttu-id="56d7a-729">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="56d7a-729">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span>
* <span data-ttu-id="56d7a-730">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="56d7a-730">Exceptions are rare, mostly special cases within the framework itself.</span></span>
* <span data-ttu-id="56d7a-731">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="56d7a-731">DI is an *alternative* to static/global object access patterns.</span></span>
* <span data-ttu-id="56d7a-732">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="56d7a-732">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>
* <span data-ttu-id="56d7a-733">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="56d7a-733">Additional resources</span></span>

::: moniker-end
