---
title: Inserción de dependencias en ASP.NET Core
author: rick-anderson
description: Obtenga información sobre la manera en que ASP.NET Core implementa la inserción de dependencias y cómo se usa.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 34ed08a5b49b56fd37628032ac73fe03a34448e6
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240855"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="b784f-103">Inserción de dependencias en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b784f-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="b784f-104">Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) y [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="b784f-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b784f-105">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b784f-106">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b784f-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b784f-107">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b784f-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b784f-108">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b784f-108">Overview of dependency injection</span></span>

<span data-ttu-id="b784f-109">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="b784f-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b784f-110">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="b784f-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b784f-111">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b784f-112">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="b784f-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b784f-113">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="b784f-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b784f-114">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="b784f-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b784f-115">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b784f-116">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b784f-117">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b784f-118">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="b784f-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b784f-119">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="b784f-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b784f-120">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="b784f-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b784f-121">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b784f-122">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b784f-123">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="b784f-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b784f-124">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b784f-125">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="b784f-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b784f-126">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="b784f-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b784f-127">En la [aplicación de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b784f-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b784f-128">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="b784f-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b784f-129">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="b784f-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b784f-130">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="b784f-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b784f-131">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b784f-132">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="b784f-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b784f-133">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="b784f-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b784f-134">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b784f-135">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b784f-136">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b784f-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b784f-137">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="b784f-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b784f-138">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="b784f-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b784f-139">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="b784f-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b784f-140">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="b784f-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b784f-141">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="b784f-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b784f-142">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="b784f-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b784f-143">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="b784f-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b784f-144">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b784f-145">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="b784f-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b784f-146">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="b784f-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b784f-147">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b784f-148">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="b784f-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b784f-149">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="b784f-149">Services injected into Startup</span></span>

<span data-ttu-id="b784f-150">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="b784f-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b784f-151">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b784f-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b784f-152">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b784f-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b784f-153">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="b784f-153">Framework-provided services</span></span>

<span data-ttu-id="b784f-154">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b784f-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b784f-155">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="b784f-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b784f-156">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="b784f-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b784f-157">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="b784f-157">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b784f-158">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="b784f-158">Service Type</span></span> | <span data-ttu-id="b784f-159">Período de duración</span><span class="sxs-lookup"><span data-stu-id="b784f-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b784f-160">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="b784f-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="b784f-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b784f-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b784f-164">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b784f-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b784f-166">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b784f-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b784f-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b784f-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b784f-170">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b784f-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b784f-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b784f-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-173">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b784f-174">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="b784f-174">Register additional services with extension methods</span></span>

<span data-ttu-id="b784f-175">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-175">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b784f-176">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="b784f-176">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b784f-177">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="b784f-177">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b784f-178">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="b784f-178">Service lifetimes</span></span>

<span data-ttu-id="b784f-179">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="b784f-179">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b784f-180">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b784f-180">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b784f-181">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-181">Transient</span></span>

<span data-ttu-id="b784f-182">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="b784f-182">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b784f-183">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="b784f-183">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="b784f-184">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b784f-184">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="b784f-185">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="b784f-185">Scoped</span></span>

<span data-ttu-id="b784f-186">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="b784f-186">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="b784f-187">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b784f-187">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="b784f-188">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="b784f-188">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b784f-189">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="b784f-189">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b784f-190">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="b784f-190">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b784f-191">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-191">Singleton</span></span>

<span data-ttu-id="b784f-192">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-192">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b784f-193">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="b784f-193">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b784f-194">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-194">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b784f-195">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-195">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="b784f-196">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-196">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="b784f-197">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="b784f-197">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b784f-198">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="b784f-198">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b784f-199">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="b784f-199">Service registration methods</span></span>

<span data-ttu-id="b784f-200">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="b784f-200">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b784f-201">Método</span><span class="sxs-lookup"><span data-stu-id="b784f-201">Method</span></span> | <span data-ttu-id="b784f-202">Automático</span><span class="sxs-lookup"><span data-stu-id="b784f-202">Automatic</span></span><br><span data-ttu-id="b784f-203">objeto</span><span class="sxs-lookup"><span data-stu-id="b784f-203">object</span></span><br><span data-ttu-id="b784f-204">eliminación</span><span class="sxs-lookup"><span data-stu-id="b784f-204">disposal</span></span> | <span data-ttu-id="b784f-205">Múltiple</span><span class="sxs-lookup"><span data-stu-id="b784f-205">Multiple</span></span><br><span data-ttu-id="b784f-206">implementaciones</span><span class="sxs-lookup"><span data-stu-id="b784f-206">implementations</span></span> | <span data-ttu-id="b784f-207">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="b784f-207">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b784f-208">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b784f-208">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b784f-209">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-209">Yes</span></span> | <span data-ttu-id="b784f-210">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-210">Yes</span></span> | <span data-ttu-id="b784f-211">No</span><span class="sxs-lookup"><span data-stu-id="b784f-211">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b784f-212">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="b784f-212">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b784f-213">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-213">Yes</span></span> | <span data-ttu-id="b784f-214">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-214">Yes</span></span> | <span data-ttu-id="b784f-215">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-215">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b784f-216">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b784f-216">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b784f-217">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-217">Yes</span></span> | <span data-ttu-id="b784f-218">No</span><span class="sxs-lookup"><span data-stu-id="b784f-218">No</span></span> | <span data-ttu-id="b784f-219">No</span><span class="sxs-lookup"><span data-stu-id="b784f-219">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b784f-220">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="b784f-220">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b784f-221">No</span><span class="sxs-lookup"><span data-stu-id="b784f-221">No</span></span> | <span data-ttu-id="b784f-222">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-222">Yes</span></span> | <span data-ttu-id="b784f-223">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-223">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b784f-224">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="b784f-224">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b784f-225">No</span><span class="sxs-lookup"><span data-stu-id="b784f-225">No</span></span> | <span data-ttu-id="b784f-226">No</span><span class="sxs-lookup"><span data-stu-id="b784f-226">No</span></span> | <span data-ttu-id="b784f-227">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-227">Yes</span></span> |

<span data-ttu-id="b784f-228">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="b784f-228">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b784f-229">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="b784f-229">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b784f-230">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="b784f-230">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b784f-231">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="b784f-231">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b784f-232">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="b784f-232">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b784f-233">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b784f-233">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b784f-234">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="b784f-234">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b784f-235">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-235">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b784f-236">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="b784f-236">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b784f-237">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="b784f-237">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b784f-238">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="b784f-238">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b784f-239">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="b784f-239">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b784f-240">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="b784f-240">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b784f-241">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="b784f-241">Constructor injection behavior</span></span>

<span data-ttu-id="b784f-242">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="b784f-242">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b784f-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-243"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b784f-244">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="b784f-244">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b784f-245">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="b784f-245">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b784f-246">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="b784f-246">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b784f-247">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="b784f-247">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b784f-248">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-248">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b784f-249">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b784f-249">Entity Framework contexts</span></span>

<span data-ttu-id="b784f-250">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-250">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b784f-251">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b784f-251">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b784f-252">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-252">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b784f-253">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="b784f-253">Lifetime and registration options</span></span>

<span data-ttu-id="b784f-254">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="b784f-254">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b784f-255">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="b784f-255">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b784f-256">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="b784f-256">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b784f-257">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="b784f-257">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b784f-258">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="b784f-258">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b784f-259">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="b784f-259">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b784f-260">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="b784f-260">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b784f-261">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="b784f-261">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b784f-262">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="b784f-262">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b784f-263">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-263">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b784f-264">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="b784f-264">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b784f-265">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-265">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b784f-266">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="b784f-266">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b784f-267">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b784f-267">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b784f-268">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="b784f-268">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b784f-269">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="b784f-269">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b784f-270">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="b784f-270">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b784f-271">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="b784f-271">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b784f-272">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="b784f-272">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b784f-273">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="b784f-273">**First request:**</span></span>

<span data-ttu-id="b784f-274">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="b784f-274">Controller operations:</span></span>

<span data-ttu-id="b784f-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b784f-275">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b784f-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b784f-276">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b784f-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-277">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-278">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-278">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-279">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="b784f-279">`OperationService` operations:</span></span>

<span data-ttu-id="b784f-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b784f-280">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b784f-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b784f-281">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b784f-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-282">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-283">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-283">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-284">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="b784f-284">**Second request:**</span></span>

<span data-ttu-id="b784f-285">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="b784f-285">Controller operations:</span></span>

<span data-ttu-id="b784f-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b784f-286">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b784f-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b784f-287">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b784f-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-288">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-289">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-289">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-290">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="b784f-290">`OperationService` operations:</span></span>

<span data-ttu-id="b784f-291">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b784f-291">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b784f-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b784f-292">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b784f-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-293">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-294">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-294">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-295">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="b784f-295">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b784f-296">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="b784f-296">*Transient* objects are always different.</span></span> <span data-ttu-id="b784f-297">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-297">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b784f-298">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-298">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b784f-299">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-299">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b784f-300">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-300">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b784f-301">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="b784f-301">Call services from main</span></span>

<span data-ttu-id="b784f-302">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-302">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b784f-303">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="b784f-303">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b784f-304">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b784f-304">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b784f-305">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="b784f-305">Scope validation</span></span>

<span data-ttu-id="b784f-306">Cuando la aplicación se ejecuta en el entorno de desarrollo y llama a [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) para compilar el host, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b784f-306">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b784f-307">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="b784f-307">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b784f-308">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="b784f-308">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b784f-309">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="b784f-309">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b784f-310">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="b784f-310">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b784f-311">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="b784f-311">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b784f-312">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="b784f-312">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b784f-313">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="b784f-313">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b784f-314">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="b784f-314">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b784f-315">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="b784f-315">Request Services</span></span>

<span data-ttu-id="b784f-316">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="b784f-316">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b784f-317">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-317">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b784f-318">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-318">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b784f-319">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="b784f-319">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b784f-320">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-320">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="b784f-321">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="b784f-321">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b784f-322">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-322">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b784f-323">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b784f-323">Design services for dependency injection</span></span>

<span data-ttu-id="b784f-324">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="b784f-324">Best practices are to:</span></span>

* <span data-ttu-id="b784f-325">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-325">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b784f-326">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="b784f-326">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b784f-327">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="b784f-327">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b784f-328">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-328">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b784f-329">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="b784f-329">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b784f-330">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="b784f-330">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b784f-331">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="b784f-331">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b784f-332">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="b784f-332">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b784f-333">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="b784f-333">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b784f-334">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="b784f-334">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b784f-335">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="b784f-335">Disposal of services</span></span>

<span data-ttu-id="b784f-336">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="b784f-336">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b784f-337">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="b784f-337">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b784f-338">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="b784f-338">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b784f-339">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b784f-339">In the following example:</span></span>

* <span data-ttu-id="b784f-340">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-340">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b784f-341">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-341">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b784f-342">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="b784f-342">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b784f-343">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-343">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="b784f-344">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="b784f-344">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="b784f-345">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="b784f-345">Transient, limited lifetime</span></span>

<span data-ttu-id="b784f-346">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="b784f-346">**Scenario**</span></span>

<span data-ttu-id="b784f-347">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="b784f-347">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="b784f-348">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="b784f-348">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="b784f-349">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="b784f-349">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="b784f-350">**Solución**</span><span class="sxs-lookup"><span data-stu-id="b784f-350">**Solution**</span></span>

<span data-ttu-id="b784f-351">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="b784f-351">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="b784f-352">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="b784f-352">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="b784f-353">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="b784f-353">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="b784f-354">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="b784f-354">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="b784f-355">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-355">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="b784f-356">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="b784f-356">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="b784f-357">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="b784f-357">**Scenario**</span></span>

<span data-ttu-id="b784f-358">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="b784f-358">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="b784f-359">**Solución**</span><span class="sxs-lookup"><span data-stu-id="b784f-359">**Solution**</span></span>

<span data-ttu-id="b784f-360">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="b784f-360">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="b784f-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="b784f-361">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="b784f-362">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="b784f-362">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="b784f-363">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="b784f-363">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="b784f-364">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="b784f-364">General Guidelines</span></span>

* <span data-ttu-id="b784f-365">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="b784f-365">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="b784f-366">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="b784f-366">Use the factory pattern instead.</span></span>
* <span data-ttu-id="b784f-367">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="b784f-367">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="b784f-368">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="b784f-368">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="b784f-369">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="b784f-369">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="b784f-370">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="b784f-370">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="b784f-371">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-371">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="b784f-372">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="b784f-372">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b784f-373">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="b784f-373">Default service container replacement</span></span>

<span data-ttu-id="b784f-374">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="b784f-374">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b784f-375">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="b784f-375">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b784f-376">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="b784f-376">Property injection</span></span>
* <span data-ttu-id="b784f-377">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="b784f-377">Injection based on name</span></span>
* <span data-ttu-id="b784f-378">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="b784f-378">Child containers</span></span>
* <span data-ttu-id="b784f-379">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="b784f-379">Custom lifetime management</span></span>
* <span data-ttu-id="b784f-380">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="b784f-380">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b784f-381">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="b784f-381">Convention-based registration</span></span>

<span data-ttu-id="b784f-382">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b784f-382">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b784f-383">Autofac</span><span class="sxs-lookup"><span data-stu-id="b784f-383">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b784f-384">DryIoc</span><span class="sxs-lookup"><span data-stu-id="b784f-384">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b784f-385">Grace</span><span class="sxs-lookup"><span data-stu-id="b784f-385">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b784f-386">LightInject</span><span class="sxs-lookup"><span data-stu-id="b784f-386">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b784f-387">Lamar</span><span class="sxs-lookup"><span data-stu-id="b784f-387">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b784f-388">Stashbox</span><span class="sxs-lookup"><span data-stu-id="b784f-388">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b784f-389">Unity</span><span class="sxs-lookup"><span data-stu-id="b784f-389">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b784f-390">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="b784f-390">Thread safety</span></span>

<span data-ttu-id="b784f-391">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="b784f-391">Create thread-safe singleton services.</span></span> <span data-ttu-id="b784f-392">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="b784f-392">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b784f-393">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="b784f-393">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b784f-394">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="b784f-394">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b784f-395">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="b784f-395">Recommendations</span></span>

* <span data-ttu-id="b784f-396">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="b784f-396">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b784f-397">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="b784f-397">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b784f-398">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-398">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b784f-399">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-399">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b784f-400">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="b784f-400">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b784f-401">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="b784f-401">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b784f-402">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-402">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b784f-403">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="b784f-403">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b784f-404">Evite el uso del *patrón del localizador de servicios*.</span><span class="sxs-lookup"><span data-stu-id="b784f-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="b784f-405">Por ejemplo, no invoque a <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio si puede usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="b784f-405">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="b784f-406">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="b784f-406">**Incorrect:**</span></span>

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

  <span data-ttu-id="b784f-407">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="b784f-407">**Correct**:</span></span>

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

* <span data-ttu-id="b784f-408">Otra variación del localizador de servicios que se debe evitar es insertar una fábrica que resuelva dependencias en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="b784f-408">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="b784f-409">Estas dos prácticas combinan estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="b784f-409">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="b784f-410">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="b784f-410">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b784f-411">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="b784f-411">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b784f-412">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="b784f-412">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b784f-413">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="b784f-413">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b784f-414">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="b784f-414">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="b784f-415">Patrones recomendados para los servicios multiinquilino en la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b784f-415">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="b784f-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) proporciona servicios multiinquilino.</span><span class="sxs-lookup"><span data-stu-id="b784f-416">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="b784f-417">Para obtener más información, vea la [documentación de Orchard Core](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="b784f-417">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="b784f-418">Vea las aplicaciones de ejemplo en https://github.com/OrchardCMS/OrchardCore.Samples para obtener ejemplos sobre cómo compilar aplicaciones modulares y multiinquilino mediante el uso exclusivo del marco de Orchard Core, sin ninguna de las características específicas de CMS.</span><span class="sxs-lookup"><span data-stu-id="b784f-418">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b784f-419">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b784f-419">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b784f-420">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b784f-420">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="b784f-421">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="b784f-421">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="b784f-422">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="b784f-422">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="b784f-423">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="b784f-423">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b784f-424">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b784f-424">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b784f-425">ASP.NET Core admite el patrón de diseño de software de inserción de dependencias (DI), que es una técnica para conseguir la [inversión de control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre clases y sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-425">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="b784f-426">Para más información específica sobre la inserción de dependencias en los controladores MVC, vea <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="b784f-426">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="b784f-427">[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b784f-427">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="b784f-428">Información general sobre la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b784f-428">Overview of dependency injection</span></span>

<span data-ttu-id="b784f-429">Una *dependencia* es cualquier objeto requerido por otro objeto.</span><span class="sxs-lookup"><span data-stu-id="b784f-429">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="b784f-430">Examine la siguiente clase `MyDependency` con un método `WriteMessage` del que dependen otras clases de una aplicación:</span><span class="sxs-lookup"><span data-stu-id="b784f-430">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="b784f-431">Se puede crear una instancia de la clase `MyDependency` para hacer que el método `WriteMessage` esté disponible para una clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-431">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="b784f-432">La clase `MyDependency` es una dependencia de la clase `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="b784f-432">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="b784f-433">La clase crea y depende directamente de la instancia `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="b784f-433">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="b784f-434">Las dependencias de código (como en el ejemplo anterior) son problemáticas y deben evitarse por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="b784f-434">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="b784f-435">Para reemplazar `MyDependency` con una implementación diferente, se debe modificar la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-435">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="b784f-436">Si `MyDependency` tiene dependencias, deben configurarse según la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-436">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="b784f-437">En un proyecto grande con varias clases que dependen de `MyDependency`, el código de configuración se dispersa por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-437">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="b784f-438">Esta implementación es difícil para realizar pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="b784f-438">This implementation is difficult to unit test.</span></span> <span data-ttu-id="b784f-439">La aplicación debe usar una clase `MyDependency` como boceto o código auxiliar, que no es posible con este enfoque.</span><span class="sxs-lookup"><span data-stu-id="b784f-439">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="b784f-440">La inserción de dependencias aborda estos problemas mediante:</span><span class="sxs-lookup"><span data-stu-id="b784f-440">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="b784f-441">Uso de una interfaz o clase base para abstraer la implementación de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-441">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="b784f-442">Registro de la dependencia en un contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-442">Registration of the dependency in a service container.</span></span> <span data-ttu-id="b784f-443">ASP.NET Core proporciona un contenedor de servicios integrado, <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="b784f-443">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="b784f-444">Los servicios se registran en el método `Startup.ConfigureServices` de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-444">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="b784f-445">*Inserción* del servicio en el constructor de la clase en la que se usa.</span><span class="sxs-lookup"><span data-stu-id="b784f-445">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="b784f-446">El marco de trabajo asume la responsabilidad de crear una instancia de la dependencia y de desecharla cuando ya no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="b784f-446">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="b784f-447">En la [aplicación de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), la interfaz `IMyDependency` define un método que el servicio proporciona a la aplicación:</span><span class="sxs-lookup"><span data-stu-id="b784f-447">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="b784f-448">Esta interfaz se implementa mediante un tipo concreto, `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="b784f-448">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="b784f-449">`MyDependency` solicita <xref:Microsoft.Extensions.Logging.ILogger`1> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="b784f-449">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="b784f-450">No es raro usar la inserción de dependencias de forma encadenada.</span><span class="sxs-lookup"><span data-stu-id="b784f-450">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="b784f-451">Cada dependencia solicitada a su vez solicita sus propias dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-451">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="b784f-452">El contenedor resuelve las dependencias del gráfico y devuelve el servicio totalmente resuelto.</span><span class="sxs-lookup"><span data-stu-id="b784f-452">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="b784f-453">El conjunto colectivo de dependencias que deben resolverse suele denominarse *árbol de dependencias*, *gráfico de dependencias* o *gráfico de objetos*.</span><span class="sxs-lookup"><span data-stu-id="b784f-453">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="b784f-454">`IMyDependency` y `ILogger<TCategoryName>` deben estar registrados en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-454">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="b784f-455">`IMyDependency` está registrado en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-455">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b784f-456">`ILogger<TCategoryName>` está registrado en la infraestructura de abstracciones de registros, por lo que se trata de un [servicio proporcionado por el marco de trabajo](#framework-provided-services) registrado de forma predeterminada por el marco de trabajo.</span><span class="sxs-lookup"><span data-stu-id="b784f-456">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="b784f-457">El contenedor resuelve `ILogger<TCategoryName>` aprovechando las ventajas de los [tipos abiertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), lo que elimina la necesidad de registrar todos los [tipos construidos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="b784f-457">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="b784f-458">En la aplicación de ejemplo, el servicio `IMyDependency` está registrado con el tipo concreto `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="b784f-458">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="b784f-459">El registro abarca la duración del servicio como la duración de una única solicitud.</span><span class="sxs-lookup"><span data-stu-id="b784f-459">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="b784f-460">Las [duraciones del servicio](#service-lifetimes) se describen más adelante en este tema.</span><span class="sxs-lookup"><span data-stu-id="b784f-460">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="b784f-461">Cada método de extensión `services.Add{SERVICE_NAME}` agrega servicios (y potencialmente los configura).</span><span class="sxs-lookup"><span data-stu-id="b784f-461">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="b784f-462">Por ejemplo, `services.AddMvc()` agrega los servicios que Razor Pages y MVC requieren.</span><span class="sxs-lookup"><span data-stu-id="b784f-462">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="b784f-463">Se recomienda que las aplicaciones sigan esta convención.</span><span class="sxs-lookup"><span data-stu-id="b784f-463">We recommended that apps follow this convention.</span></span> <span data-ttu-id="b784f-464">Coloque los métodos de extensión en el espacio de nombres [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-464">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="b784f-465">Si el constructor del servicio requiere un [tipo integrado](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, se puede insertar mediante la [configuración](xref:fundamentals/configuration/index) o el [patrón de opciones](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="b784f-465">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="b784f-466">Se solicita una instancia del servicio mediante el constructor de una clase, en la que se usa el servicio y se asigna a un campo privado.</span><span class="sxs-lookup"><span data-stu-id="b784f-466">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="b784f-467">El campo de utiliza para acceder al servicio, según sea necesario en la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-467">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="b784f-468">En la aplicación de ejemplo, la instancia `IMyDependency` se solicita y usa para llamar al método `WriteMessage` del servicio:</span><span class="sxs-lookup"><span data-stu-id="b784f-468">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="b784f-469">Servicios insertados en Startup</span><span class="sxs-lookup"><span data-stu-id="b784f-469">Services injected into Startup</span></span>

<span data-ttu-id="b784f-470">Solo se pueden insertar los tipos de servicio siguientes en el constructor `Startup` cuando se usa el host genérico (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span><span class="sxs-lookup"><span data-stu-id="b784f-470">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="b784f-471">Los servicios se pueden insertar en `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b784f-471">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="b784f-472">Para obtener más información, vea <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="b784f-472">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="b784f-473">Servicios proporcionados por el marco de trabajo</span><span class="sxs-lookup"><span data-stu-id="b784f-473">Framework-provided services</span></span>

<span data-ttu-id="b784f-474">El método `Startup.ConfigureServices` se encarga de definir los servicios que la aplicación usa, incluidas las características de plataforma, como Entity Framework Core y ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="b784f-474">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="b784f-475">Inicialmente, el valor `IServiceCollection` proporcionado a `ConfigureServices` tiene los servicios definidos por el marco en función de [cómo se configurara el host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="b784f-475">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="b784f-476">No es raro que una aplicación basada en una plantilla de ASP.NET Core tenga cientos de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="b784f-476">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="b784f-477">En la tabla siguiente se ilustra una pequeña muestra de servicios registrados por el marco.</span><span class="sxs-lookup"><span data-stu-id="b784f-477">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="b784f-478">Tipo de servicio</span><span class="sxs-lookup"><span data-stu-id="b784f-478">Service Type</span></span> | <span data-ttu-id="b784f-479">Período de duración</span><span class="sxs-lookup"><span data-stu-id="b784f-479">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="b784f-480">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-480">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="b784f-481">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-481">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="b784f-482">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-482">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="b784f-483">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-483">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="b784f-484">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-484">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="b784f-485">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-485">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="b784f-486">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-486">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="b784f-487">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-487">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="b784f-488">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-488">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="b784f-489">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-489">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="b784f-490">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-490">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="b784f-491">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-491">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="b784f-492">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-492">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="b784f-493">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-493">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="b784f-494">Registro de servicios adicionales con métodos de extensión</span><span class="sxs-lookup"><span data-stu-id="b784f-494">Register additional services with extension methods</span></span>

<span data-ttu-id="b784f-495">Cuando un método de extensión de la colección de servicio está disponible para registrar un servicio (y sus servicios dependientes, si es necesario), la convención consiste en usar un solo método de extensión `Add{SERVICE_NAME}` para registrar todos los servicios requeridos por dicho servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-495">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="b784f-496">El código siguiente es un ejemplo de cómo agregar servicios adicionales al contenedor mediante los métodos de extensión [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) y <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="b784f-496">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="b784f-497">Para obtener más información, consulte la clase <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> en la documentación de la API.</span><span class="sxs-lookup"><span data-stu-id="b784f-497">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="b784f-498">Duraciones de servicios</span><span class="sxs-lookup"><span data-stu-id="b784f-498">Service lifetimes</span></span>

<span data-ttu-id="b784f-499">Elija una duración adecuada para cada servicio registrado.</span><span class="sxs-lookup"><span data-stu-id="b784f-499">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="b784f-500">Los servicios de ASP.NET Core pueden configurarse con las duraciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b784f-500">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="b784f-501">Transitorio</span><span class="sxs-lookup"><span data-stu-id="b784f-501">Transient</span></span>

<span data-ttu-id="b784f-502">Los servicios de duración transitoria (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se crean cada vez que el contenedor del servicio los solicita.</span><span class="sxs-lookup"><span data-stu-id="b784f-502">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="b784f-503">Esta duración funciona mejor para servicios sin estado ligeros.</span><span class="sxs-lookup"><span data-stu-id="b784f-503">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="b784f-504">En las aplicaciones que procesan solicitudes, los servicios transitorios se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b784f-504">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="b784f-505">Con ámbito</span><span class="sxs-lookup"><span data-stu-id="b784f-505">Scoped</span></span>

<span data-ttu-id="b784f-506">Los servicios de duración con ámbito (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se crean una vez por solicitud del cliente (conexión).</span><span class="sxs-lookup"><span data-stu-id="b784f-506">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="b784f-507">En las aplicaciones que procesan solicitudes, los servicios con ámbito se eliminan al final de la solicitud.</span><span class="sxs-lookup"><span data-stu-id="b784f-507">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="b784f-508">Si usa un servicio con ámbito en un middleware, inserte el servicio en el método `Invoke` o `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="b784f-508">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="b784f-509">No lo inserte a través de la [inserción de constructores](xref:mvc/controllers/dependency-injection#constructor-injection), porque ello hace que el servicio se comporte como un singleton.</span><span class="sxs-lookup"><span data-stu-id="b784f-509">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="b784f-510">Para obtener más información, vea <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="b784f-510">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="b784f-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="b784f-511">Singleton</span></span>

<span data-ttu-id="b784f-512">Los servicios con duración Singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) se crean la primera vez que se solicitan, o bien al ejecutar `Startup.ConfigureServices` y especificar una instancia con el registro del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-512">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="b784f-513">Cada solicitud posterior usa la misma instancia.</span><span class="sxs-lookup"><span data-stu-id="b784f-513">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="b784f-514">Si la aplicación requiere un comportamiento de singleton, se recomienda permitir que el contenedor de servicios administre la duración del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-514">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="b784f-515">No implemente el patrón de diseño de singleton y proporcione el código de usuario para administrar la duración del objeto en la clase.</span><span class="sxs-lookup"><span data-stu-id="b784f-515">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="b784f-516">En las aplicaciones que procesan solicitudes, los servicios singleton se eliminan cuando <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> se elimina al cerrarse la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-516">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="b784f-517">Es peligroso resolver un servicio con ámbito desde un singleton.</span><span class="sxs-lookup"><span data-stu-id="b784f-517">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="b784f-518">Puede dar lugar a que el servicio adopte un estado incorrecto al procesar solicitudes posteriores.</span><span class="sxs-lookup"><span data-stu-id="b784f-518">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="b784f-519">Métodos de registro del servicio</span><span class="sxs-lookup"><span data-stu-id="b784f-519">Service registration methods</span></span>

<span data-ttu-id="b784f-520">Los métodos de extensión de registro del servicio ofrecen sobrecargas útiles en escenarios específicos.</span><span class="sxs-lookup"><span data-stu-id="b784f-520">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="b784f-521">Método</span><span class="sxs-lookup"><span data-stu-id="b784f-521">Method</span></span> | <span data-ttu-id="b784f-522">Automático</span><span class="sxs-lookup"><span data-stu-id="b784f-522">Automatic</span></span><br><span data-ttu-id="b784f-523">objeto</span><span class="sxs-lookup"><span data-stu-id="b784f-523">object</span></span><br><span data-ttu-id="b784f-524">eliminación</span><span class="sxs-lookup"><span data-stu-id="b784f-524">disposal</span></span> | <span data-ttu-id="b784f-525">Múltiple</span><span class="sxs-lookup"><span data-stu-id="b784f-525">Multiple</span></span><br><span data-ttu-id="b784f-526">implementaciones</span><span class="sxs-lookup"><span data-stu-id="b784f-526">implementations</span></span> | <span data-ttu-id="b784f-527">Transferencia de argumentos</span><span class="sxs-lookup"><span data-stu-id="b784f-527">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="b784f-528">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b784f-528">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="b784f-529">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-529">Yes</span></span> | <span data-ttu-id="b784f-530">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-530">Yes</span></span> | <span data-ttu-id="b784f-531">No</span><span class="sxs-lookup"><span data-stu-id="b784f-531">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="b784f-532">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="b784f-532">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="b784f-533">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-533">Yes</span></span> | <span data-ttu-id="b784f-534">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-534">Yes</span></span> | <span data-ttu-id="b784f-535">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-535">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="b784f-536">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b784f-536">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="b784f-537">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-537">Yes</span></span> | <span data-ttu-id="b784f-538">No</span><span class="sxs-lookup"><span data-stu-id="b784f-538">No</span></span> | <span data-ttu-id="b784f-539">No</span><span class="sxs-lookup"><span data-stu-id="b784f-539">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="b784f-540">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="b784f-540">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="b784f-541">No</span><span class="sxs-lookup"><span data-stu-id="b784f-541">No</span></span> | <span data-ttu-id="b784f-542">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-542">Yes</span></span> | <span data-ttu-id="b784f-543">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-543">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="b784f-544">Ejemplos:</span><span class="sxs-lookup"><span data-stu-id="b784f-544">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="b784f-545">No</span><span class="sxs-lookup"><span data-stu-id="b784f-545">No</span></span> | <span data-ttu-id="b784f-546">No</span><span class="sxs-lookup"><span data-stu-id="b784f-546">No</span></span> | <span data-ttu-id="b784f-547">Sí</span><span class="sxs-lookup"><span data-stu-id="b784f-547">Yes</span></span> |

<span data-ttu-id="b784f-548">Para obtener más información sobre el tipo de eliminación, consulte la sección [Eliminación de servicios](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="b784f-548">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="b784f-549">Un escenario común para varias implementaciones es [utilizar tipos de simulación para las pruebas](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="b784f-549">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="b784f-550">Los métodos `TryAdd{LIFETIME}` solo registran el servicio si no hay ya una implementación registrada.</span><span class="sxs-lookup"><span data-stu-id="b784f-550">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="b784f-551">En el ejemplo siguiente, la primera línea registra `MyDependency` para `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="b784f-551">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="b784f-552">La segunda línea no tiene ningún efecto porque `IMyDependency` ya tiene una implementación registrada:</span><span class="sxs-lookup"><span data-stu-id="b784f-552">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="b784f-553">Para obtener más información, consulte:</span><span class="sxs-lookup"><span data-stu-id="b784f-553">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="b784f-554">Los métodos [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registran solo el servicio si no hay ya una implementación *del mismo tipo*.</span><span class="sxs-lookup"><span data-stu-id="b784f-554">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="b784f-555">A través de `IEnumerable<{SERVICE}>` se resuelven varios servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-555">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="b784f-556">Al registrar los servicios, el desarrollador solo quiere agregar una instancia si no se ha agregado ya una del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="b784f-556">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="b784f-557">Por lo general, este método lo utilizan los creadores de bibliotecas para evitar registrar dos copias de una instancia en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="b784f-557">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="b784f-558">En el ejemplo siguiente, la primera línea registra `MyDep` para `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="b784f-558">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="b784f-559">La segunda línea registra `MyDep` para `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="b784f-559">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="b784f-560">La tercera línea no tiene ningún efecto porque `IMyDep1` ya tiene una implementación registrada de `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="b784f-560">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="b784f-561">Comportamiento de inserción de constructor</span><span class="sxs-lookup"><span data-stu-id="b784f-561">Constructor injection behavior</span></span>

<span data-ttu-id="b784f-562">Los servicios se pueden resolver mediante dos mecanismos:</span><span class="sxs-lookup"><span data-stu-id="b784f-562">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="b784f-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: permite la creación de objetos sin registrar el servicio en el contenedor de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-563"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="b784f-564">`ActivatorUtilities` se utiliza con abstracciones orientadas al usuario, como asistentes de etiquetas, controladores MVC y enlazadores de modelos.</span><span class="sxs-lookup"><span data-stu-id="b784f-564">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="b784f-565">Los constructores pueden aceptar argumentos que no se proporcionan mediante la inserción de dependencias, pero los argumentos deben asignar valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="b784f-565">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="b784f-566">Cuando se resuelven los servicios mediante `IServiceProvider` o `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere un constructor *público*.</span><span class="sxs-lookup"><span data-stu-id="b784f-566">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="b784f-567">Cuando se resuelven los servicios mediante `ActivatorUtilities`, la [inserción del constructor](xref:mvc/controllers/dependency-injection#constructor-injection) requiere que exista solo un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="b784f-567">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="b784f-568">Se admiten las sobrecargas de constructor, pero solo puede existir una sobrecarga cuyos argumentos pueda cumplir la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-568">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="b784f-569">Contextos de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b784f-569">Entity Framework contexts</span></span>

<span data-ttu-id="b784f-570">Los contextos de Entity Framework normalmente se agregan al contenedor de servicios mediante la [duración con ámbito](#service-lifetimes) porque las operaciones de base de datos de aplicación web se suelen limitar a la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-570">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="b784f-571">La duración predeterminada se limita si no se especifica una duración mediante una sobrecarga de [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) al registrar el contexto de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b784f-571">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="b784f-572">En los servicios de una duración determinada no se debe usar un contexto de base de datos con una duración más corta que el servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-572">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="b784f-573">Opciones de registro y duración</span><span class="sxs-lookup"><span data-stu-id="b784f-573">Lifetime and registration options</span></span>

<span data-ttu-id="b784f-574">Para mostrar la diferencia entre la duración y las opciones de registro, considere las siguientes interfaces que representan tareas como una operación con un identificador único, `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="b784f-574">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="b784f-575">Según cómo esté configurada la duración de un servicio de operaciones para las interfaces siguientes, el contenedor proporciona la misma instancia del servicio u otra distinta cuando así lo solicita la clase:</span><span class="sxs-lookup"><span data-stu-id="b784f-575">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="b784f-576">Las interfaces se implementan en la clase `Operation`.</span><span class="sxs-lookup"><span data-stu-id="b784f-576">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="b784f-577">El constructor `Operation` genera un GUID en caso de que no se proporcione uno:</span><span class="sxs-lookup"><span data-stu-id="b784f-577">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="b784f-578">Se registra una instancia de `OperationService` que depende de cada uno de los demás tipos `Operation`.</span><span class="sxs-lookup"><span data-stu-id="b784f-578">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="b784f-579">Cuando `OperationService` se solicita con la inserción de dependencias, recibe una instancia nueva de cada servicio o una instancia existente en función de la duración de los servicios dependientes.</span><span class="sxs-lookup"><span data-stu-id="b784f-579">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="b784f-580">Si se crean servicios transitorios cuando se solicitan al contenedor, el elemento `OperationId` del servicio `IOperationTransient` es diferente del objeto `OperationId` de `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="b784f-580">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="b784f-581">`OperationService` recibe una instancia nueva de la clase `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="b784f-581">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="b784f-582">La nueva instancia produce un objeto `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="b784f-582">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="b784f-583">Si se crean servicios con ámbito por solicitud de cliente, el objeto `OperationId` del servicio `IOperationScoped` es el mismo que para `OperationService` dentro de la solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-583">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="b784f-584">Entre las solicitudes de cliente, ambos servicios comparten un valor `OperationId` diferente.</span><span class="sxs-lookup"><span data-stu-id="b784f-584">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="b784f-585">Si se crean servicios singleton y servicios de instancia singleton una vez y se usan en todas las solicitudes de cliente y servicios, el objeto `OperationId` es constante en todas las solicitudes de servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-585">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="b784f-586">En `Startup.ConfigureServices`, cada tipo se agrega al contenedor según su duración con nombre:</span><span class="sxs-lookup"><span data-stu-id="b784f-586">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="b784f-587">El servicio `IOperationSingletonInstance` usa una instancia específica con un identificador conocido de `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="b784f-587">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="b784f-588">Resulta evidente identificar cuándo este tipo está en uso (su GUID es todo ceros).</span><span class="sxs-lookup"><span data-stu-id="b784f-588">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="b784f-589">La aplicación de ejemplo muestra las duraciones de los objetos dentro y entre las solicitudes individuales.</span><span class="sxs-lookup"><span data-stu-id="b784f-589">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="b784f-590">El objeto `IndexModel` de la aplicación de ejemplo solicita cada tipo de `IOperation` y `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="b784f-590">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="b784f-591">Después, la página muestra todos los valores `OperationId` del servicio y la clase del modelo de página mediante las asignaciones de propiedades:</span><span class="sxs-lookup"><span data-stu-id="b784f-591">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="b784f-592">Las dos salidas siguientes muestran el resultado de dos solicitudes:</span><span class="sxs-lookup"><span data-stu-id="b784f-592">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="b784f-593">**Primera solicitud:**</span><span class="sxs-lookup"><span data-stu-id="b784f-593">**First request:**</span></span>

<span data-ttu-id="b784f-594">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="b784f-594">Controller operations:</span></span>

<span data-ttu-id="b784f-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="b784f-595">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="b784f-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b784f-596">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b784f-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-597">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-598">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-598">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-599">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="b784f-599">`OperationService` operations:</span></span>

<span data-ttu-id="b784f-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="b784f-600">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="b784f-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="b784f-601">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="b784f-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-602">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-603">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-603">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-604">**Segunda solicitud:**</span><span class="sxs-lookup"><span data-stu-id="b784f-604">**Second request:**</span></span>

<span data-ttu-id="b784f-605">Operaciones del controlador:</span><span class="sxs-lookup"><span data-stu-id="b784f-605">Controller operations:</span></span>

<span data-ttu-id="b784f-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="b784f-606">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="b784f-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b784f-607">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b784f-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-608">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-609">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-609">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-610">Operaciones `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="b784f-610">`OperationService` operations:</span></span>

<span data-ttu-id="b784f-611">Transitorio: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="b784f-611">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="b784f-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="b784f-612">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="b784f-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="b784f-613">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="b784f-614">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="b784f-614">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="b784f-615">Observe cuál de los valores `OperationId` varía dentro de una solicitud y entre solicitudes:</span><span class="sxs-lookup"><span data-stu-id="b784f-615">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="b784f-616">Los objetos *Transient* siempre son diferentes.</span><span class="sxs-lookup"><span data-stu-id="b784f-616">*Transient* objects are always different.</span></span> <span data-ttu-id="b784f-617">El valor `OperationId` transitorio de la primera y la segunda solicitud de cliente varía tanto para las operaciones `OperationService` como entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-617">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="b784f-618">Se proporciona una nueva instancia para cada solicitud de servicio y solicitud de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-618">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="b784f-619">Los objetos *con ámbito* son iguales dentro de una solicitud de cliente, pero varían entre solicitudes de cliente.</span><span class="sxs-lookup"><span data-stu-id="b784f-619">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="b784f-620">Los objetos *singleton* son iguales para todos los objetos y solicitudes, independientemente de si se proporciona una instancia `Operation` en `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-620">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="b784f-621">Llamada a servicios desde main</span><span class="sxs-lookup"><span data-stu-id="b784f-621">Call services from main</span></span>

<span data-ttu-id="b784f-622">Cree un elemento <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> con [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) para resolver un servicio con ámbito dentro del ámbito de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-622">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="b784f-623">Este método resulta útil para tener acceso a un servicio con ámbito durante el inicio para realizar tareas de inicialización.</span><span class="sxs-lookup"><span data-stu-id="b784f-623">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="b784f-624">En el siguiente ejemplo se indica cómo obtener un contexto para `MyScopedService` en `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b784f-624">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="b784f-625">Validación del ámbito</span><span class="sxs-lookup"><span data-stu-id="b784f-625">Scope validation</span></span>

<span data-ttu-id="b784f-626">Cuando la aplicación se ejecuta en el entorno de desarrollo, el proveedor de servicios predeterminado realiza comprobaciones para confirmar lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b784f-626">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="b784f-627">Los servicios con ámbito no se resuelven directa o indirectamente desde el proveedor de servicios raíz.</span><span class="sxs-lookup"><span data-stu-id="b784f-627">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="b784f-628">Los servicios con ámbito no se insertan directa o indirectamente en singletons.</span><span class="sxs-lookup"><span data-stu-id="b784f-628">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="b784f-629">El proveedor de servicios raíz se crea cuando se llama a <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="b784f-629">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="b784f-630">La vigencia del proveedor de servicios raíz es la misma que la de la aplicación o el servidor cuando el proveedor se inicia con la aplicación, y se elimina cuando la aplicación se cierra.</span><span class="sxs-lookup"><span data-stu-id="b784f-630">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="b784f-631">De la eliminación de los servicios con ámbito se encarga el contenedor que los creó.</span><span class="sxs-lookup"><span data-stu-id="b784f-631">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="b784f-632">Si un servicio con ámbito se crea en el contenedor raíz, su vigencia sube a la del singleton, ya que solo lo puede eliminar el contenedor raíz cuando la aplicación o el servidor se cierran.</span><span class="sxs-lookup"><span data-stu-id="b784f-632">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="b784f-633">Al validar los ámbitos de servicio, este tipo de situaciones se detectan cuando se llama a `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="b784f-633">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="b784f-634">Para obtener más información, vea <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="b784f-634">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="b784f-635">Servicios de solicitud</span><span class="sxs-lookup"><span data-stu-id="b784f-635">Request Services</span></span>

<span data-ttu-id="b784f-636">Los servicios disponibles en una solicitud de ASP.NET Core desde `HttpContext` se exponen mediante la colección [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="b784f-636">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="b784f-637">Los servicios de solicitud representan los servicios configurados y solicitados como parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-637">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="b784f-638">Cuando los objetos especifican dependencias, estas se cumplen mediante los tipos que se encuentran en `RequestServices`, no en `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-638">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="b784f-639">Por lo general, la aplicación no debe usar estas propiedades directamente.</span><span class="sxs-lookup"><span data-stu-id="b784f-639">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="b784f-640">En su lugar, solicite los tipos que las clases requieren mediante el constructor de clases y permita que el marco de trabajo inserte las dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-640">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="b784f-641">Esto produce clases que son más fáciles de probar.</span><span class="sxs-lookup"><span data-stu-id="b784f-641">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="b784f-642">Se recomienda que solicite las dependencias como parámetros del constructor para obtener acceso a la colección `RequestServices`.</span><span class="sxs-lookup"><span data-stu-id="b784f-642">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="b784f-643">Diseño de servicios para la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="b784f-643">Design services for dependency injection</span></span>

<span data-ttu-id="b784f-644">Los procedimientos recomendados son:</span><span class="sxs-lookup"><span data-stu-id="b784f-644">Best practices are to:</span></span>

* <span data-ttu-id="b784f-645">Diseñar servicios para usar la inserción de dependencias a fin de obtener sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-645">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="b784f-646">Evitar clases y miembros estáticos y con estado.</span><span class="sxs-lookup"><span data-stu-id="b784f-646">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="b784f-647">En su lugar, diseñe las aplicaciones para usar servicios Singleton, lo que evita crear un estado global.</span><span class="sxs-lookup"><span data-stu-id="b784f-647">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="b784f-648">Evitar la creación directa de instancias de clases dependientes dentro de los servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-648">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="b784f-649">La creación directa de instancias se acopla al código de una implementación particular.</span><span class="sxs-lookup"><span data-stu-id="b784f-649">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="b784f-650">Cree clases de aplicación pequeñas, bien factorizadas y probadas con facilidad.</span><span class="sxs-lookup"><span data-stu-id="b784f-650">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="b784f-651">Si una clase parece tener demasiadas dependencias insertadas, suele indicar que la clase tiene demasiadas responsabilidades y que esto infringe el [principio de responsabilidad única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="b784f-651">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="b784f-652">Trate de mover algunas de las responsabilidades de la clase a una nueva para intentar refactorizarla.</span><span class="sxs-lookup"><span data-stu-id="b784f-652">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="b784f-653">Tenga en cuenta que las clases del modelo de página de Razor Pages y las clases del controlador MVC deben centrarse en aspectos de la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="b784f-653">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="b784f-654">Los detalles de implementación de las reglas de negocio y del acceso a datos se deben mantener en las clases pertinentes para [cada uno de estos aspectos](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="b784f-654">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="b784f-655">Eliminación de servicios</span><span class="sxs-lookup"><span data-stu-id="b784f-655">Disposal of services</span></span>

<span data-ttu-id="b784f-656">El contenedor llama a <xref:System.IDisposable.Dispose*> para los tipos <xref:System.IDisposable> que crea.</span><span class="sxs-lookup"><span data-stu-id="b784f-656">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="b784f-657">Si se agrega una instancia al contenedor por código de usuario, no se elimina automáticamente.</span><span class="sxs-lookup"><span data-stu-id="b784f-657">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="b784f-658">En el ejemplo siguiente, el contenedor de servicios crea los servicios y se eliminan de manera automática:</span><span class="sxs-lookup"><span data-stu-id="b784f-658">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="b784f-659">En el ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b784f-659">In the following example:</span></span>

* <span data-ttu-id="b784f-660">El contenedor de servicios no crea las instancias de servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-660">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="b784f-661">El marco de trabajo no conoce la duración prevista del servicio.</span><span class="sxs-lookup"><span data-stu-id="b784f-661">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="b784f-662">El marco de trabajo no elimina los servicios de manera automática.</span><span class="sxs-lookup"><span data-stu-id="b784f-662">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="b784f-663">Si los servicios no se eliminan explícitamente en el código de desarrollador, se conservan hasta que se cierra la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b784f-663">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="b784f-664">Instrucciones de IDisposable para instancias transitorias y compartidas</span><span class="sxs-lookup"><span data-stu-id="b784f-664">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="b784f-665">Instancia transitoria, duración limitada</span><span class="sxs-lookup"><span data-stu-id="b784f-665">Transient, limited lifetime</span></span>

<span data-ttu-id="b784f-666">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="b784f-666">**Scenario**</span></span>

<span data-ttu-id="b784f-667">La aplicación requiere una instancia de <xref:System.IDisposable> con una duración transitoria para cualquiera de estos escenarios:</span><span class="sxs-lookup"><span data-stu-id="b784f-667">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="b784f-668">La instancia se resuelve en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="b784f-668">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="b784f-669">La instancia se debe eliminar antes de que finalice el ámbito.</span><span class="sxs-lookup"><span data-stu-id="b784f-669">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="b784f-670">**Solución**</span><span class="sxs-lookup"><span data-stu-id="b784f-670">**Solution**</span></span>

<span data-ttu-id="b784f-671">Use el patrón de fábrica para crear una instancia fuera del ámbito principal.</span><span class="sxs-lookup"><span data-stu-id="b784f-671">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="b784f-672">En esta situación, la aplicación habitualmente tendría un método `Create` que llama directamente al constructor del tipo final.</span><span class="sxs-lookup"><span data-stu-id="b784f-672">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="b784f-673">Si el tipo final tiene otras dependencias, la fábrica puede:</span><span class="sxs-lookup"><span data-stu-id="b784f-673">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="b784f-674">Recibir un <xref:System.IServiceProvider> en su constructor.</span><span class="sxs-lookup"><span data-stu-id="b784f-674">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="b784f-675">Usar <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> para crear instancias de la instancia fuera del contenedor, mientras usa el contenedor para sus dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-675">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="b784f-676">Instancia compartida, duración limitada</span><span class="sxs-lookup"><span data-stu-id="b784f-676">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="b784f-677">**Escenario**</span><span class="sxs-lookup"><span data-stu-id="b784f-677">**Scenario**</span></span>

<span data-ttu-id="b784f-678">La aplicación requiere una instancia de <xref:System.IDisposable> compartida en varios servicios, pero <xref:System.IDisposable> debe tener una duración limitada.</span><span class="sxs-lookup"><span data-stu-id="b784f-678">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="b784f-679">**Solución**</span><span class="sxs-lookup"><span data-stu-id="b784f-679">**Solution**</span></span>

<span data-ttu-id="b784f-680">Registre la instancia con una duración restringida.</span><span class="sxs-lookup"><span data-stu-id="b784f-680">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="b784f-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> para iniciar y crear un <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> nuevo.</span><span class="sxs-lookup"><span data-stu-id="b784f-681">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="b784f-682">Use el <xref:System.IServiceProvider> del ámbito para obtener los servicios necesarios.</span><span class="sxs-lookup"><span data-stu-id="b784f-682">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="b784f-683">Elimine el ámbito cuando la duración deba finalizar.</span><span class="sxs-lookup"><span data-stu-id="b784f-683">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="b784f-684">Instrucciones generales</span><span class="sxs-lookup"><span data-stu-id="b784f-684">General Guidelines</span></span>

* <span data-ttu-id="b784f-685">No registre instancias de <xref:System.IDisposable> con un ámbito transitorio.</span><span class="sxs-lookup"><span data-stu-id="b784f-685">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="b784f-686">En su lugar, use el patrón de fábrica.</span><span class="sxs-lookup"><span data-stu-id="b784f-686">Use the factory pattern instead.</span></span>
* <span data-ttu-id="b784f-687">No resuelva instancias de <xref:System.IDisposable> transitorias ni restringidas en el ámbito raíz.</span><span class="sxs-lookup"><span data-stu-id="b784f-687">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="b784f-688">La única excepción general es cuando la aplicación crea o vuelve a crear el <xref:System.IServiceProvider> o lo elimina, lo que no es un patrón ideal.</span><span class="sxs-lookup"><span data-stu-id="b784f-688">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="b784f-689">La recepción de una dependencia de <xref:System.IDisposable> a través de las inserciones de dependencias no requiere que el receptor implemente <xref:System.IDisposable> por sí mismo.</span><span class="sxs-lookup"><span data-stu-id="b784f-689">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="b784f-690">El receptor de la dependencia de <xref:System.IDisposable> no debe llamar a <xref:System.IDisposable.Dispose%2A> en esa dependencia.</span><span class="sxs-lookup"><span data-stu-id="b784f-690">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="b784f-691">Se deben usar ámbitos para controlar la duración de los servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-691">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="b784f-692">Los ámbitos no son jerárquicos y no hay ninguna conexión especial entre ellos.</span><span class="sxs-lookup"><span data-stu-id="b784f-692">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="b784f-693">Reemplazo del contenedor de servicios predeterminado</span><span class="sxs-lookup"><span data-stu-id="b784f-693">Default service container replacement</span></span>

<span data-ttu-id="b784f-694">El contenedor de servicios integrado está diseñado para atender las necesidades del marco y de la mayoría de las aplicaciones de consumidor.</span><span class="sxs-lookup"><span data-stu-id="b784f-694">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="b784f-695">Se recomienda usar el contenedor integrado a menos que necesite una característica específica que el contenedor integrado no admite, como las siguientes:</span><span class="sxs-lookup"><span data-stu-id="b784f-695">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="b784f-696">Inserción de propiedades</span><span class="sxs-lookup"><span data-stu-id="b784f-696">Property injection</span></span>
* <span data-ttu-id="b784f-697">Inserción basada en nombres</span><span class="sxs-lookup"><span data-stu-id="b784f-697">Injection based on name</span></span>
* <span data-ttu-id="b784f-698">Contenedores secundarios</span><span class="sxs-lookup"><span data-stu-id="b784f-698">Child containers</span></span>
* <span data-ttu-id="b784f-699">Administración personalizada del ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="b784f-699">Custom lifetime management</span></span>
* <span data-ttu-id="b784f-700">Compatibilidad con `Func<T>` para la inicialización diferida</span><span class="sxs-lookup"><span data-stu-id="b784f-700">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="b784f-701">Registro basado en convenciones</span><span class="sxs-lookup"><span data-stu-id="b784f-701">Convention-based registration</span></span>

<span data-ttu-id="b784f-702">Los siguientes contenedores de terceros se pueden usar con aplicaciones ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b784f-702">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="b784f-703">Autofac</span><span class="sxs-lookup"><span data-stu-id="b784f-703">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="b784f-704">DryIoc</span><span class="sxs-lookup"><span data-stu-id="b784f-704">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="b784f-705">Grace</span><span class="sxs-lookup"><span data-stu-id="b784f-705">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="b784f-706">LightInject</span><span class="sxs-lookup"><span data-stu-id="b784f-706">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="b784f-707">Lamar</span><span class="sxs-lookup"><span data-stu-id="b784f-707">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="b784f-708">Stashbox</span><span class="sxs-lookup"><span data-stu-id="b784f-708">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="b784f-709">Unity</span><span class="sxs-lookup"><span data-stu-id="b784f-709">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="b784f-710">Seguridad para subprocesos</span><span class="sxs-lookup"><span data-stu-id="b784f-710">Thread safety</span></span>

<span data-ttu-id="b784f-711">Cree servicios de singleton seguros para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="b784f-711">Create thread-safe singleton services.</span></span> <span data-ttu-id="b784f-712">Si un servicio de singleton tiene una dependencia en un servicio transitorio, es posible que este también deba ser seguro para subprocesos, según cómo lo use el singleton.</span><span class="sxs-lookup"><span data-stu-id="b784f-712">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="b784f-713">El patrón de diseño Factory Method de un servicio único, como el segundo argumento para [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), no necesita ser seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="b784f-713">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="b784f-714">Al igual que un constructor de tipos (`static`), se garantiza que se le llame una vez mediante un único subproceso.</span><span class="sxs-lookup"><span data-stu-id="b784f-714">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="b784f-715">Recomendaciones</span><span class="sxs-lookup"><span data-stu-id="b784f-715">Recommendations</span></span>

* <span data-ttu-id="b784f-716">No se admite la resolución de servicio basada en `async/await` y `Task`.</span><span class="sxs-lookup"><span data-stu-id="b784f-716">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="b784f-717">C# no admite los constructores asincrónicos, por lo que el patrón recomendado es usar métodos asincrónicos después de resolver el servicio de manera sincrónica.</span><span class="sxs-lookup"><span data-stu-id="b784f-717">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="b784f-718">Evite almacenar datos y configuraciones directamente en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-718">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="b784f-719">Por ejemplo, el carro de la compra de un usuario no debería agregarse al contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="b784f-719">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="b784f-720">La configuración debe usar el [patrón de opciones](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="b784f-720">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b784f-721">Del mismo modo, evite los objetos de tipo "contenedor de datos" que solo existen para permitir el acceso a otro objeto.</span><span class="sxs-lookup"><span data-stu-id="b784f-721">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="b784f-722">Es mejor solicitar el elemento real que se necesita mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="b784f-722">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="b784f-723">Evite el acceso estático a servicios (por ejemplo, escribiendo de forma estática [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) para usarlo en otro lugar).</span><span class="sxs-lookup"><span data-stu-id="b784f-723">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="b784f-724">Evite usar el *patrón de localizador de servicios*, que combina las estrategias de [Inversión de control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="b784f-724">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="b784f-725">No invoque <xref:System.IServiceProvider.GetService*> para obtener una instancia de servicio cuando pueda usar la inserción de dependencias en su lugar:</span><span class="sxs-lookup"><span data-stu-id="b784f-725">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="b784f-726">**Incorrecto:**</span><span class="sxs-lookup"><span data-stu-id="b784f-726">**Incorrect:**</span></span>

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

    <span data-ttu-id="b784f-727">**Correcto**:</span><span class="sxs-lookup"><span data-stu-id="b784f-727">**Correct**:</span></span>

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

  * <span data-ttu-id="b784f-728">Evite insertar una fábrica que resuelva las dependencias en tiempo de ejecución con <xref:System.IServiceProvider.GetService*>.</span><span class="sxs-lookup"><span data-stu-id="b784f-728">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="b784f-729">Evite el acceso estático a `HttpContext` (por ejemplo, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="b784f-729">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="b784f-730">Al igual que sucede con todas las recomendaciones, podría verse en una situación que le obligue a ignorar alguna de ellas.</span><span class="sxs-lookup"><span data-stu-id="b784f-730">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="b784f-731">Las excepciones son poco frecuentes, principalmente en casos especiales dentro del marco de trabajo mismo.</span><span class="sxs-lookup"><span data-stu-id="b784f-731">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="b784f-732">La inserción de dependencias es una *alternativa* a los patrones de acceso a objetos estáticos o globales.</span><span class="sxs-lookup"><span data-stu-id="b784f-732">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="b784f-733">No podrá aprovechar las ventajas de la inserción de dependencias si la combina con el acceso a objetos estáticos.</span><span class="sxs-lookup"><span data-stu-id="b784f-733">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b784f-734">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="b784f-734">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="b784f-735">Cuatro formas de eliminar IDisposables en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b784f-735">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="b784f-736">Escritura de código limpio en ASP.NET Core con inserción de dependencias (MSDN)</span><span class="sxs-lookup"><span data-stu-id="b784f-736">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="b784f-737">Principio de dependencias explícitas</span><span class="sxs-lookup"><span data-stu-id="b784f-737">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="b784f-738">Los contenedores de inversión de control y el patrón de inserción de dependencias (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="b784f-738">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="b784f-739">Cómo registrar un servicio con varias interfaces de inserción de dependencias de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b784f-739">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
