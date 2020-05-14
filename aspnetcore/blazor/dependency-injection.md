---
title: Inserción de dependencias de Blazor de ASP.NET Core
author: guardrex
description: Vea cómo las aplicaciones Blazor pueden insertar servicios en los componentes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: e96698bd0bd8f3f3b290ba24bc8169efb16f1d03
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967537"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="17e57-103">Inserción de dependencias de Blazor de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17e57-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="17e57-104">Por [Rainer Stropek](https://www.timecockpit.com) y [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="17e57-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="17e57-105">Blazor es compatible con la [inserción de dependencias (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="17e57-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="17e57-106">Las aplicaciones pueden usar servicios integrados mediante su inserción en componentes.</span><span class="sxs-lookup"><span data-stu-id="17e57-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="17e57-107">Las aplicaciones también pueden definir y registrar servicios personalizados y hacer que estén disponibles en toda la aplicación a través de la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17e57-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="17e57-108">La inserción de dependencias es una técnica para acceder a los servicios configurados en una ubicación central.</span><span class="sxs-lookup"><span data-stu-id="17e57-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="17e57-109">Esto puede resultar en las aplicaciones Blazor para:</span><span class="sxs-lookup"><span data-stu-id="17e57-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="17e57-110">Compartir una única instancia de una clase de servicio entre varios componentes, lo que se conoce como servicio *singleton*.</span><span class="sxs-lookup"><span data-stu-id="17e57-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="17e57-111">Desacoplar componentes de clases de servicio concretas mediante abstracciones de referencia.</span><span class="sxs-lookup"><span data-stu-id="17e57-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="17e57-112">Por ejemplo, considere una interfaz `IDataAccess` para acceder a los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17e57-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="17e57-113">La interfaz se implementa mediante una clase `DataAccess` concreta y se registra como un servicio en el contenedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17e57-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="17e57-114">Cuando un componente usa la inserción de dependencias para recibir una implementación de `IDataAccess`, el componente no se acopla al tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="17e57-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="17e57-115">La implementación se puede intercambiar, posiblemente para una implementación ficticia en pruebas unitarias.</span><span class="sxs-lookup"><span data-stu-id="17e57-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="17e57-116">Servicios predeterminados</span><span class="sxs-lookup"><span data-stu-id="17e57-116">Default services</span></span>

<span data-ttu-id="17e57-117">Los servicios predeterminados se agregan de forma automática a la colección de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17e57-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="17e57-118">web de Office</span><span class="sxs-lookup"><span data-stu-id="17e57-118">Service</span></span> | <span data-ttu-id="17e57-119">Período de duración</span><span class="sxs-lookup"><span data-stu-id="17e57-119">Lifetime</span></span> | <span data-ttu-id="17e57-120">Descripción</span><span class="sxs-lookup"><span data-stu-id="17e57-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="17e57-121">Transitorio</span><span class="sxs-lookup"><span data-stu-id="17e57-121">Transient</span></span> | <span data-ttu-id="17e57-122">Proporciona métodos para enviar solicitudes HTTP y recibir respuestas HTTP de un recurso identificado por un URI.</span><span class="sxs-lookup"><span data-stu-id="17e57-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="17e57-123">La instancia de `HttpClient` en una aplicación WebAssembly de Blazor usa el explorador para administrar el tráfico HTTP en segundo plano.</span><span class="sxs-lookup"><span data-stu-id="17e57-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="17e57-124">Las aplicaciones de servidor Blazor no incluyen un objeto `HttpClient` configurado como servicio de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="17e57-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="17e57-125">Proporcione un objeto `HttpClient` a una aplicación de servidor Blazor.</span><span class="sxs-lookup"><span data-stu-id="17e57-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="17e57-126">Para obtener más información, vea <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="17e57-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="17e57-127">Singleton (WebAssembly de Blazor)</span><span class="sxs-lookup"><span data-stu-id="17e57-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="17e57-128">Con ámbito (servidor Blazor)</span><span class="sxs-lookup"><span data-stu-id="17e57-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="17e57-129">Representa una instancia de un entorno de ejecución de JavaScript en la que se envían las llamadas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="17e57-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="17e57-130">Para obtener más información, vea <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="17e57-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| `NavigationManager` | <span data-ttu-id="17e57-131">Singleton (WebAssembly de Blazor)</span><span class="sxs-lookup"><span data-stu-id="17e57-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="17e57-132">Con ámbito (servidor Blazor)</span><span class="sxs-lookup"><span data-stu-id="17e57-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="17e57-133">Contiene asistentes para trabajar con URI y el estado de navegación.</span><span class="sxs-lookup"><span data-stu-id="17e57-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="17e57-134">Para obtener más información, vea [Asistentes de URI y estado de navegación](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="17e57-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="17e57-135">Un proveedor de servicios personalizado no proporciona automáticamente los servicios predeterminados que aparecen en la tabla.</span><span class="sxs-lookup"><span data-stu-id="17e57-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="17e57-136">Si usa un proveedor de servicios personalizado y necesita cualquiera de los servicios que se muestran en la tabla, agregue los servicios necesarios al nuevo proveedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="17e57-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="17e57-137">Adición de servicios a una aplicación</span><span class="sxs-lookup"><span data-stu-id="17e57-137">Add services to an app</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="17e57-138">WebAssembly de Blazor</span><span class="sxs-lookup"><span data-stu-id="17e57-138">Blazor WebAssembly</span></span>

<span data-ttu-id="17e57-139">Configure los servicios de la colección de servicios de la aplicación en el método `Main` de *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="17e57-139">Configure services for the app's service collection in the `Main` method of *Program.cs*.</span></span> <span data-ttu-id="17e57-140">En el ejemplo siguiente, la implementación de `MyDependency` se registra para `IMyDependency`:</span><span class="sxs-lookup"><span data-stu-id="17e57-140">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="17e57-141">Una vez que se ha compilado el host, se puede acceder a los servicios desde el ámbito de inserción de dependencias raíz antes de que se representen los componentes.</span><span class="sxs-lookup"><span data-stu-id="17e57-141">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="17e57-142">Esto puede ser útil para ejecutar la lógica de inicialización antes de representar el contenido:</span><span class="sxs-lookup"><span data-stu-id="17e57-142">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="17e57-143">El host también proporciona una instancia de configuración central para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17e57-143">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="17e57-144">En función del ejemplo anterior, la dirección URL del servicio meteorológico se pasa de un origen de configuración predeterminado (por ejemplo, *appsettings.json*) a `InitializeWeatherAsync`:</span><span class="sxs-lookup"><span data-stu-id="17e57-144">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, *appsettings.json*) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a><span data-ttu-id="17e57-145">Servidor Blazor</span><span class="sxs-lookup"><span data-stu-id="17e57-145">Blazor Server</span></span>

<span data-ttu-id="17e57-146">Después de crear una aplicación, examine el método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="17e57-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="17e57-147">Al método `ConfigureServices` se le pasa una interfaz <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, que es una lista de objetos de descriptor de servicio (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="17e57-147">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="17e57-148">Para agregar los servicios, se proporcionan descriptores de servicio a la colección de servicios.</span><span class="sxs-lookup"><span data-stu-id="17e57-148">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="17e57-149">En el ejemplo siguiente se muestra el concepto con la interfaz `IDataAccess` y su implementación concreta de `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="17e57-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="17e57-150">Duración del servicio</span><span class="sxs-lookup"><span data-stu-id="17e57-150">Service lifetime</span></span>

<span data-ttu-id="17e57-151">Los servicios se pueden configurar con las duraciones que se muestran en la tabla siguiente.</span><span class="sxs-lookup"><span data-stu-id="17e57-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="17e57-152">Período de duración</span><span class="sxs-lookup"><span data-stu-id="17e57-152">Lifetime</span></span> | <span data-ttu-id="17e57-153">Descripción</span><span class="sxs-lookup"><span data-stu-id="17e57-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="17e57-154">Las aplicaciones WebAssembly de Blazor no tienen actualmente un concepto de ámbitos de inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17e57-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="17e57-155">Los servicios registrados con `Scoped` se comportan como servicios `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="17e57-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="17e57-156">Pero el modelo de hospedaje del servidor Blazor admite la duración `Scoped`.</span><span class="sxs-lookup"><span data-stu-id="17e57-156">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="17e57-157">En las aplicaciones de servidor Blazor, el ámbito del registro de un servicio con ámbito es la *conexión*.</span><span class="sxs-lookup"><span data-stu-id="17e57-157">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="17e57-158">Por este motivo, se prefiere el uso de servicios con ámbito para los servicios que deben tener el ámbito del usuario actual, aunque la intención actual sea ejecutar el lado cliente en el explorador.</span><span class="sxs-lookup"><span data-stu-id="17e57-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="17e57-159">La inserción de dependencias crea una *sola instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="17e57-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="17e57-160">Todos los componentes que requieren un servicio `Singleton` reciben una instancia del mismo servicio.</span><span class="sxs-lookup"><span data-stu-id="17e57-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="17e57-161">Cada vez que un componente obtiene una instancia de un servicio `Transient` del contenedor de servicios, recibe una *nueva instancia* del servicio.</span><span class="sxs-lookup"><span data-stu-id="17e57-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="17e57-162">El sistema de inserción de dependencias se basa en el sistema de inserción de dependencias de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="17e57-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="17e57-163">Para obtener más información, vea <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="17e57-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="17e57-164">Solicitud de un servicio en un componente</span><span class="sxs-lookup"><span data-stu-id="17e57-164">Request a service in a component</span></span>

<span data-ttu-id="17e57-165">Una vez que se han agregado los servicios a la colección de servicios, insértelos en los componentes mediante la directiva [\@inject](xref:mvc/views/razor#inject) de Razor.</span><span class="sxs-lookup"><span data-stu-id="17e57-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="17e57-166">`@inject` tiene dos parámetros:</span><span class="sxs-lookup"><span data-stu-id="17e57-166">`@inject` has two parameters:</span></span>

* <span data-ttu-id="17e57-167">Type: el tipo de servicio que se va a insertar.</span><span class="sxs-lookup"><span data-stu-id="17e57-167">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="17e57-168">Property: el nombre de la propiedad que recibe el servicio de aplicación insertado.</span><span class="sxs-lookup"><span data-stu-id="17e57-168">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="17e57-169">La propiedad no requiere la creación manual.</span><span class="sxs-lookup"><span data-stu-id="17e57-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="17e57-170">El compilador crea la propiedad.</span><span class="sxs-lookup"><span data-stu-id="17e57-170">The compiler creates the property.</span></span>

<span data-ttu-id="17e57-171">Para obtener más información, vea <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="17e57-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="17e57-172">Use varias instrucciones `@inject` para insertar distintos servicios.</span><span class="sxs-lookup"><span data-stu-id="17e57-172">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="17e57-173">En el ejemplo siguiente se muestra cómo utilizar `@inject`.</span><span class="sxs-lookup"><span data-stu-id="17e57-173">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="17e57-174">El servicio que implementa `Services.IDataAccess` se inserta en la propiedad `DataRepository` del componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="17e57-175">Observe cómo el código solo usa la abstracción de `IDataAccess`:</span><span class="sxs-lookup"><span data-stu-id="17e57-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="17e57-176">De forma interna, la propiedad generada (`DataRepository`) usa el atributo `InjectAttribute`.</span><span class="sxs-lookup"><span data-stu-id="17e57-176">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="17e57-177">Normalmente, este atributo no se usa de manera directa.</span><span class="sxs-lookup"><span data-stu-id="17e57-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="17e57-178">Si se necesita una clase base para los componentes y las propiedades insertadas también son necesarias para la clase base, agregue manualmente `InjectAttribute`:</span><span class="sxs-lookup"><span data-stu-id="17e57-178">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="17e57-179">En los componentes derivados de la clase base, la directiva `@inject` no es necesaria.</span><span class="sxs-lookup"><span data-stu-id="17e57-179">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="17e57-180">Es suficiente con el objeto `InjectAttribute` de la clase base:</span><span class="sxs-lookup"><span data-stu-id="17e57-180">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="17e57-181">Uso de la inserción de dependencias en servicios</span><span class="sxs-lookup"><span data-stu-id="17e57-181">Use DI in services</span></span>

<span data-ttu-id="17e57-182">Es posible que los servicios complejos requieran servicios adicionales.</span><span class="sxs-lookup"><span data-stu-id="17e57-182">Complex services might require additional services.</span></span> <span data-ttu-id="17e57-183">En el ejemplo anterior, `DataAccess` podría requerir el servicio predeterminado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="17e57-183">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="17e57-184">`@inject` (o `InjectAttribute`) no está disponible para su uso en los servicios.</span><span class="sxs-lookup"><span data-stu-id="17e57-184">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="17e57-185">En su lugar se debe usar la *inserción de constructores*.</span><span class="sxs-lookup"><span data-stu-id="17e57-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="17e57-186">Los servicios necesarios se agregan mediante la adición de parámetros al constructor del servicio.</span><span class="sxs-lookup"><span data-stu-id="17e57-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="17e57-187">Cuando la inserción de dependencias crea el servicio, reconoce los servicios que requiere en el constructor y los proporciona en consecuencia.</span><span class="sxs-lookup"><span data-stu-id="17e57-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="17e57-188">Requisitos previos para la inserción de constructores:</span><span class="sxs-lookup"><span data-stu-id="17e57-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="17e57-189">Debe existir un constructor cuyos argumentos se puedan cumplir mediante la inserción de dependencias.</span><span class="sxs-lookup"><span data-stu-id="17e57-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="17e57-190">Se permiten parámetros adicionales que no estén incluidos en la inserción de dependencias si especifican valores predeterminados.</span><span class="sxs-lookup"><span data-stu-id="17e57-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="17e57-191">El constructor aplicable debe ser *público*.</span><span class="sxs-lookup"><span data-stu-id="17e57-191">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="17e57-192">Debe existir un constructor aplicable.</span><span class="sxs-lookup"><span data-stu-id="17e57-192">One applicable constructor must exist.</span></span> <span data-ttu-id="17e57-193">En caso de ambigüedad, la inserción de dependencias inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="17e57-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="17e57-194">Clases de componentes base de utilidad para administrar un ámbito de inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="17e57-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="17e57-195">En las aplicaciones ASP.NET Core, el ámbito de los servicios con ámbito suele ser el de la solicitud actual.</span><span class="sxs-lookup"><span data-stu-id="17e57-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="17e57-196">Una vez que se ha completado la solicitud, el sistema de inserción de dependencias elimina todos los servicios con ámbito o transitorios.</span><span class="sxs-lookup"><span data-stu-id="17e57-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="17e57-197">En las aplicaciones de servidor Blazor, el ámbito de la solicitud dura lo mismo que la conexión de cliente, lo que puede dar lugar a que los servicios transitorios y con ámbito duren mucho más de lo esperado.</span><span class="sxs-lookup"><span data-stu-id="17e57-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="17e57-198">En las aplicaciones WebAssembly de Blazor, los servicios registrados con una duración con ámbito se tratan como singleton, por lo que viven más que los servicios con ámbito de aplicaciones ASP.NET Core típicas.</span><span class="sxs-lookup"><span data-stu-id="17e57-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

<span data-ttu-id="17e57-199">Un enfoque que limita la duración de un servicio en las aplicaciones Blazor es el uso del tipo `OwningComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="17e57-199">An approach that limits a service lifetime in Blazor apps is use of the `OwningComponentBase` type.</span></span> <span data-ttu-id="17e57-200">`OwningComponentBase` es un tipo abstracto derivado de `ComponentBase` que crea un ámbito de inserción de dependencias que se corresponde a la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-200">`OwningComponentBase` is an abstract type derived from `ComponentBase` that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="17e57-201">Con este ámbito, es posible usar los servicios de inserción de dependencias con una duración con ámbito y hacer que duren lo mismo que el componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-201">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="17e57-202">Cuando el componente se destruye, también se eliminan los servicios del proveedor de servicios con ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-202">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="17e57-203">Esto puede ser útil para servicios que:</span><span class="sxs-lookup"><span data-stu-id="17e57-203">This can be useful for services that:</span></span>

* <span data-ttu-id="17e57-204">Se deban reutilizar dentro de un componente, ya que la duración transitoria no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="17e57-204">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="17e57-205">No se deban compartir entre componentes, ya que la duración de singleton no es apropiada.</span><span class="sxs-lookup"><span data-stu-id="17e57-205">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="17e57-206">Hay dos versiones del tipo `OwningComponentBase` disponibles:</span><span class="sxs-lookup"><span data-stu-id="17e57-206">Two versions of the `OwningComponentBase` type are available:</span></span>

* <span data-ttu-id="17e57-207">`OwningComponentBase` es un elemento secundario abstracto y descartable del tipo `ComponentBase` con una propiedad `ScopedServices` protegida de tipo `IServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="17e57-207">`OwningComponentBase` is an abstract, disposable child of the `ComponentBase` type with a protected `ScopedServices` property of type `IServiceProvider`.</span></span> <span data-ttu-id="17e57-208">Este proveedor se puede usar para resolver los servicios cuyo ámbito es la duración del componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-208">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="17e57-209">Los servicios de inserción de dependencias insertados en el componente mediante `@inject` o `InjectAttribute` (`[Inject]`) no se crean en el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-209">DI services injected into the component using `@inject` or the `InjectAttribute` (`[Inject]`) aren't created in the component's scope.</span></span> <span data-ttu-id="17e57-210">Para usar el ámbito del componente, los servicios se deben resolver mediante `ScopedServices.GetRequiredService` o `ScopedServices.GetService`.</span><span class="sxs-lookup"><span data-stu-id="17e57-210">To use the component's scope, services must be resolved using `ScopedServices.GetRequiredService` or `ScopedServices.GetService`.</span></span> <span data-ttu-id="17e57-211">Los servicios que se resuelvan mediante el proveedor de `ScopedServices` reciben sus dependencias desde ese mismo ámbito.</span><span class="sxs-lookup"><span data-stu-id="17e57-211">Any services resolved using the `ScopedServices` provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="17e57-212">`OwningComponentBase<T>` deriva de `OwningComponentBase` y agrega una propiedad `Service` que devuelve una instancia de `T` desde el proveedor de inserción de dependencias con ámbito.</span><span class="sxs-lookup"><span data-stu-id="17e57-212">`OwningComponentBase<T>` derives from `OwningComponentBase` and adds a property `Service` that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="17e57-213">Este tipo es una manera cómoda de acceder a los servicios con ámbito sin usar una instancia de `IServiceProvider` cuando hay un servicio principal que la aplicación necesita del contenedor de inserción de dependencias que usa el ámbito del componente.</span><span class="sxs-lookup"><span data-stu-id="17e57-213">This type is a convenient way to access scoped services without using an instance of `IServiceProvider` when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="17e57-214">La propiedad `ScopedServices` está disponible, por lo que la aplicación puede obtener servicios de otros tipos, si es necesario.</span><span class="sxs-lookup"><span data-stu-id="17e57-214">The `ScopedServices` property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a><span data-ttu-id="17e57-215">Uso de DbContext de Entity Framework desde la inserción de dependencias</span><span class="sxs-lookup"><span data-stu-id="17e57-215">Use of Entity Framework DbContext from DI</span></span>

<span data-ttu-id="17e57-216">Un tipo de servicio común que se puede recuperar desde la inserción de dependencias en aplicaciones web es el objeto `DbContext` de Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="17e57-216">One common service type to retrieve from DI in web apps is Entity Framework (EF) `DbContext` objects.</span></span> <span data-ttu-id="17e57-217">El registro de servicios de EF mediante `IServiceCollection.AddDbContext` agrega el objeto `DbContext` como un servicio con ámbito de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="17e57-217">Registering EF services using `IServiceCollection.AddDbContext` adds the `DbContext` as a scoped service by default.</span></span> <span data-ttu-id="17e57-218">El registro como un servicio con ámbito puede dar lugar a problemas en aplicaciones Blazor porque hace que las instancias de `DbContext` sean de larga duración y se compartan a través de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="17e57-218">Registering as a scoped service can lead to problems in Blazor apps because it causes `DbContext` instances to be long-lived and shared across the app.</span></span> <span data-ttu-id="17e57-219">`DbContext` no es seguro para subprocesos y no se debe usar de manera simultánea.</span><span class="sxs-lookup"><span data-stu-id="17e57-219">`DbContext` isn't thread-safe and must not be used concurrently.</span></span>

<span data-ttu-id="17e57-220">En función de la aplicación, el uso de `OwningComponentBase` para limitar el ámbito de un objeto `DbContext` a un componente único *puede* resolver el problema.</span><span class="sxs-lookup"><span data-stu-id="17e57-220">Depending on the app, using `OwningComponentBase` to limit the scope of a `DbContext` to a single component *may* solve the issue.</span></span> <span data-ttu-id="17e57-221">Si un componente no usa un objeto `DbContext` en paralelo, es suficiente con la derivación del componente de `OwningComponentBase` y la recuperación del objeto `DbContext` de `ScopedServices` porque garantiza lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="17e57-221">If a component doesn't use a `DbContext` in parallel, deriving the component from `OwningComponentBase` and retrieving the `DbContext` from `ScopedServices` is sufficient because it ensures that:</span></span>

* <span data-ttu-id="17e57-222">Los componentes independientes no comparten un objeto `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="17e57-222">Separate components don't share a `DbContext`.</span></span>
* <span data-ttu-id="17e57-223">El objeto `DbContext` dura tanto como el componente que depende de él.</span><span class="sxs-lookup"><span data-stu-id="17e57-223">The `DbContext` lives only as long as the component depending on it.</span></span>

<span data-ttu-id="17e57-224">Si es posible que un único componente use un objeto `DbContext` de forma simultánea (por ejemplo, cada vez que un usuario selecciona un botón), incluso el uso de `OwningComponentBase` no evita problemas con las operaciones de EF simultáneas.</span><span class="sxs-lookup"><span data-stu-id="17e57-224">If a single component might use a `DbContext` concurrently (for example, every time a user selects a button), even using `OwningComponentBase` doesn't avoid issues with concurrent EF operations.</span></span> <span data-ttu-id="17e57-225">En ese caso, use otro objeto `DbContext` para cada operación de EF lógica.</span><span class="sxs-lookup"><span data-stu-id="17e57-225">In that case, use a different `DbContext` for each logical EF operation.</span></span> <span data-ttu-id="17e57-226">Use uno de los enfoques siguientes:</span><span class="sxs-lookup"><span data-stu-id="17e57-226">Use either of the following approaches:</span></span>

* <span data-ttu-id="17e57-227">Cree el objeto `DbContext` directamente con `DbContextOptions<TContext>` como argumento, que se puede recuperar mediante la inserción de dependencias y es seguro para subprocesos.</span><span class="sxs-lookup"><span data-stu-id="17e57-227">Create the `DbContext` directly using `DbContextOptions<TContext>` as an argument, which can be retrieved from DI and is thread safe.</span></span>

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* <span data-ttu-id="17e57-228">Registre el objeto `DbContext` en el contenedor de servicios con una duración transitoria:</span><span class="sxs-lookup"><span data-stu-id="17e57-228">Register the `DbContext` in the service container with a transient lifetime:</span></span>
  * <span data-ttu-id="17e57-229">Al registrar el contexto, use `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="17e57-229">When registering the context, use `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="17e57-230">El método de extensión `AddDbContext` toma dos parámetros opcionales de tipo `ServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="17e57-230">The `AddDbContext` extension method takes two optional parameters of type `ServiceLifetime`.</span></span> <span data-ttu-id="17e57-231">Para usar este enfoque, solo el parámetro `contextLifetime` tiene que ser `ServiceLifetime.Transient`.</span><span class="sxs-lookup"><span data-stu-id="17e57-231">To use this approach, only the `contextLifetime` parameter needs to be `ServiceLifetime.Transient`.</span></span> <span data-ttu-id="17e57-232">`optionsLifetime` puede conservar su valor predeterminado de `ServiceLifetime.Scoped`.</span><span class="sxs-lookup"><span data-stu-id="17e57-232">`optionsLifetime` can keep its default value of `ServiceLifetime.Scoped`.</span></span>

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * <span data-ttu-id="17e57-233">El objeto `DbContext` transitorio se puede insertar de la forma habitual (mediante `@inject`) en componentes que no ejecutarán varias operaciones de EF en paralelo.</span><span class="sxs-lookup"><span data-stu-id="17e57-233">The transient `DbContext` can be injected as normal (using `@inject`) into components that will not execute multiple EF operations in parallel.</span></span> <span data-ttu-id="17e57-234">Los que puedan realizar varias operaciones de EF simultáneamente pueden solicitar objetos `DbContext` independientes para cada operación paralela mediante `IServiceProvider.GetRequiredService`.</span><span class="sxs-lookup"><span data-stu-id="17e57-234">Those that may perform multiple EF operations simultaneously can request separate `DbContext` objects for each parallel operation using `IServiceProvider.GetRequiredService`.</span></span>

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="17e57-235">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="17e57-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
